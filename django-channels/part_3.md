# Part 3 - Deploying to amazon AWS

## STEP1 - Add Application Load Balance

1. Open Amazon EC2 [here](https://console.aws.amazon.com/ec2/)

2. (Assuming region of load balancer already selected) In the navigation pane, select **Load Balancers**

    <div style="text-align: center;">
        <img src="https://user-images.githubusercontent.com/6856382/73227490-f1d93100-4130-11ea-9fc0-6e263c876466.png">
    </div>


3. Choose **Create Load Balancer**

    <div style="text-align: center;">
        <img src="https://user-images.githubusercontent.com/6856382/73227608-4381bb80-4131-11ea-81c1-39a1f3bfc89a.png">
    </div>


4. Choose **Application Load Balancer** and then choose Continue.

  <div style="text-align: center;">
        <img src="https://user-images.githubusercontent.com/6856382/73227698-952a4600-4131-11ea-9a70-b95f5a00d07b.png">
    </div>

5. Complete the **Configure Load Balancer** page as follows:

- **Name** for the name of Load Balancer
- **Scheme**
    - Choose **internet-facing** if clinet<->internet<->server
    - Choose **internal** if between private ip addresses
    - Choose **Dual Stack** if requires combination of both IPv4 and IPv6 otherwise choose IPv4
        - choose IPv4 for simplicity
    - Change **port** 80 if wishes the listener to accept HTTP traffic in other ports
    - Choose at least 2 **Availiability Zones**

## References

1. [Django Channels on AWS Elastic Beanstalk using an ALB](https://gist.github.com/mortenege/68be5e859a6e91320be940212abfb207)
2. [Creating an Application Load Balancer](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/create-application-load-balancer.html)



