# Requirements for changing the instance type<a name="resize-requirements"></a>

To change the instance type of your Amazon EC2 instance, consider the following requirements:
+ You must select an instance type that is compatible with the configuration of the instance\. If the instance type that you want is not compatible with the instance's current configuration, then you must migrate your application to a new instance with the instance type that you need\.
+ To change the instance type, the instance must be in the `stopped` state\.
+ You cannot change the intance type if hibernation is enabled on the instance\.