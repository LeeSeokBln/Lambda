# Lambda
S3에서 이벤트가 생기면 SNS를 통하여 사용자에게 알려주는 람다 코드입니다.
```
import boto3

topic_arn = "<SNS 람다 ARN>"
def send_sns(message, subject):
    try:
        client = boto3.client("sns")
        result = client.publish(TopicArn=topic_arn, Message=message, Subject=subject)
        if result['ResponseMetadata']['HTTPStatusCode'] == 200:
            print(result)
            print("Notification send successfully..!!!")
            return True
    except Exception as e:
        print("Error occured while publish notifications and error is : ", e)
        return True

def lambda_handler(event, context):
    print("event collected is {}".format(event))
    for record in event['Records'] :
        s3_bucket = record['s3']['bucket']['name']
        print("Bucket name is {}".format(s3_bucket))
        s3_key = record['s3']['object']['key']
        print("Bucket key name is {}".format(s3_key))
        from_path = "s3://{}/{}".format(s3_bucket, s3_key)
        print("from path {}".format(from_path))
        message = "The file is uploaded at S3 bucket path {}".format(from_path)
        subject = "Processes completion Notification"
        SNSResult = send_sns(message, subject)  #보낼 메세지
        if SNSResult :
            print("Notification Sent..") 
            return SNSResult
        else:
            return False
```
