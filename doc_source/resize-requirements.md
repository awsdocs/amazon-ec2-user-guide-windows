# Requirements for changing the instance type<a name="resize-requirements"></a>

To resize your Amazon EC2 instance by changing its instance type, consider the following requirements:
+ You must select an instance type that is compatible with the configuration of the instance\. If the instance type that you want is not compatible with the instance configuration you have, then you must migrate your application to a new instance with the instance type that you need\.
+ To change the instance type, the instance must be in the `stopped` state\.
+ You cannot resize an instance if hibernation is enabled\.