# The Chainable Request Pattern

Notes from the post: [Advanced Ruby: The Chainable Request Pattern](https://thoughtbot.com/blog/advanced-ruby-the-chainable-request-pattern)

This article covers some ideas of abstraction for the transactional pattern. It shows how to encapsulate API request for their use in an external transaction, including:

- Automatic rollback on error
- Support for running custom code in between requests
- Chain multiple API requests with automatic rollback support
- Return a chained result of all API requests

The goal of this pattern is to allow you to chain multiple API requests together, where the next request is only executed if the previous one was successful. Additionally, if any request fails, all the previous successful requests are automatically rolled back.

## The Request Object

THe first step is to create a class that encaps the API request. This class must return a result object with the outcome. It's important to remember that the result object must return whether the request was successful or not.

```ruby
# First, create a class that encapsulates the API request
# Using a Struct to define the result object. It includes the status, error code, id and the keyword_init: true option to allow the use of keyword arguments
Result = Struct.new(:status, :error_code, :id, keyword_init: true) do
    def success?
        status == :ok
    end

    def error?
        !success?
    end
end

class LineItemRequest
    BASE_URL = 'https://api.example.com/line_items'
    HEADERS = { 'Content-Type' => 'application/json' }.freeze

    # The execute method sends a POST request to the API
    def self.execute(params)
        response = client.post("ledger_transactions", JSON.dump(params))

        if response.status == 200
            # If the response status is 200, the request was successful and the id is returned
            Result.new(status: :ok, id: response.body['id'])
        else
            # If the response status is not 200, the request failed and the error code is returned
            Result.new(status: :error, error_code: response.status)
        end
    end

    # The client method creates a new Faraday client with the base URL and headers
    # private_class_method is used to make the method private
    # def self. is used to define a class method
    private_class_method def self.client
        # Faraday is a popular HTTP client library for Ruby
        # Faraday.new creates a new Faraday client with the specified URL and headers
        Faraday.new(url: BASE_URL, headers: HEADERS) do |client|
            org_id = Configutation.organization_id
            api_key = Configuration.api_key

            # The client.response :json line tells Faraday to parse JSON responses
            # The client.request :authorization, :basic, org_id, api_key line sets up basic authentication with the organization ID and API key
            client.response :json
            client.request :authorization, :basic, org_id, api_key
        end
    end
end
```

## The Request Runner

The goal of the request runner is to provide a way to execute API request encapsulated in other classes (like `LineItemRequest`), while also allowing custom code to be run if the request is successful.

```ruby
class RequestRunner
    # The execute method takes a request and a block of code
    def self.execute(request, params)
        result = request.execute(params)

        # the block_given? method checks if a block of code was passed to the method
        if block_given?
            # If a block of code was passed, the block is executed with the result object as an argument
            yield result
        end

        # The result object is returned
        result
    end
end
```

Then use the request object:

```ruby
params = {
    description: 'Line item description',
    status: 'active',
    ledger_entries: [
        { amount: 100, direction: 'debit', ledger_account_id: 1 },
        { amount: 100, direction: 'credit', ledger_account_id: 2 },
    ]
}

# The execute method is called with the LineItemRequest class and the params hash
RequestRunner.execute LineItemRequest, params do |result|
    # The block of code is executed with the result object as an argument
    puts "Successfully created line item with ID: #{result.id}"
end
```

## Rollbacks

The request runner runs a request and executes a block that optionally runs dependent code. If the dependent code fails, the request runner should automatically rollback the request.

First, we need to implement a rollback method in the request object:

```ruby
class LineItemRequest
    def self.execute(params)
        # The execute method is the same as before
    end

    # The rollback method takes the result object as an argument
    def self.rollback(result, _params)
        return if result.nil?

        # Archiving is how the request is rolled back in this example
        patch_params = { status: 'archived' }
        client.patch("ledger_transactions/#{result.id}", JSON.dump(patch_params))
    end
end
```

Every request object should implement the following methods:

- `execute(params)`: Retuns a Struct result that responds to `success?`.
- `rollback(result, params)`: Returns void. Don't care for the return value.

Now, we need to hook up `rollback` to the request runner:

```ruby

class RequestRunner
    def self.execute(request, params)
        result = request.execute(params)

        if block_given? && result.success?
            begin
                yield result
            rescue StandardError => e
                # If an error occurs, the rollback method is called with the result object and params hash
                request.rollback(result, params)
                raise e
            end
        end

        result
    end
end
```

The exception handler is used because we want to revert out successful request if an exception is raised. After rollback, we still need to rreraise the exception to signal the error to the caller.
We don't rollback when the result is an error because it means that the request failed, so its side-effects were not applied.

The following step is to execute the request and try to save an Active Record reference:

```ruby
RequestRunner.execute LineItemRequest, params do result
    OrderLineItem.create! external_id: result.id
end
```

In Rails, the `create!` raises an exception if the record is not saved. If the exception is raised, the request is rolled back.
