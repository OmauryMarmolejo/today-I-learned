# About Task Definition Memory

When you are defining a task definition, you can specify the amount of memory and CPU in the task itself or in the container definitions. The task memory is the total amount of memory that is reserved for the task to run. The task memory is the sum of the memory that is reserved for the containers in the task. The task memory is the total amount of memory that is reserved for the task to run. The task memory is the sum of the memory that is reserved for the containers in the task.

## memory vs memoryReservation

When you are defining a container definition, you can specify the memory and memoryReservation parameters. The memory parameter specifies the amount of memory that is reserved for the container to run. The memoryReservation parameter specifies the amount of memory that is reserved for the container to run. The memory parameter specifies the amount of memory that is reserved for the container to run. The memoryReservation parameter specifies the amount of memory that is reserved for the container to run.

If you specify only the memory parameter, the value of the memory parameter is used for both the memory and memoryReservation parameters. If you specify only the memoryReservation parameter, the value of the memoryReservation parameter is used for both the memory and memoryReservation parameters:

```json
{
  "containerDefinitions": [
    {
      "name": "my-container",
      "image": "my-image",
      "memory": 512,
      "memoryReservation": 256
    }
  ]
}
```

In this example the cealing value of memory is 512 and the memoryReservation is 256.
