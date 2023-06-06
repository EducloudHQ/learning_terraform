After successfully deploying the infrastructure, it's only right we test to make sure 
everything works as expected. 

Firstly, let's check if the eventbridge rule was created successfully.

Sign-in to the aws console,search and open EventBridge from the search bar. 

Click on `rules` at the middle right-hand side of the screen and search for `backup---`.

![alt text](https://raw.githubusercontent.com/EducloudHQ/learning_terraform/master/assets/eventbridge_rule.png)

If the infrastructure deployed correctly, should see your eventbridge rule. 

This rule invokes a step functions state machine every `12pm` with a payload.

But for testing purposes and since we can't wait till 12pm, we'll send the payload manually as input 
to the state machine.




