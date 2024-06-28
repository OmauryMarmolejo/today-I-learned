# Dealing with XML request and their params

They are a few ways to deal with XML request in Rails. In a specific case where the params are unavailable you can search for them in the `request.body` using the `Hash.from_xml` method that Ruby provides.
Example:
Given a request to create an user:

```ruby
class UsersController < ApplicationController
    def create
        @user = User.new(user_params)
    end

    private

    # Here Hash.from_xml is used to parse the request body and extract the user params and we user the permit method to whitelist the params
    def user_params
        params = Hash.from_xml(request.body.read)["user"]
        params.permit(:name, :email)
    end
end
```

## Considerations

In most cases, you should use the `params` method to access the request parameters. The `params` method is a method provided by Rails that returns a hash with the request parameters. The `params` method is also capable of parsing XML requests and extracting the parameters from them.
