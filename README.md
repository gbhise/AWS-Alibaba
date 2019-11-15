# AWS-Alibaba
Copying data from AWS S3 to Alibaba OSS using function compute service

	import oss2
	import boto3

	def handler(event, context):

		S3_BUCKET_NAME      = 'AWS_SOURCE_BUCKET_NAME'
		AWS_KEY				= 'AWS_ACCOUNT_KEY'
		AWS_SECRET			= 'AWS_ACCOUNT_SECRET'

		OSS_KEY         	= 'ALIBABA_OSS_KEY'
		OSS_SECRET      	= 'ALIBABA_OSS_SECRET'
		OSS_BUCKET_NAME     = 'ALIBABA_DESTINATION_BUCKET_NAME'
		OSS_ENDPOINT    	= 'oss END POINT'  # For ex: 'oss-cn-beijing.aliyuncs.com'
		FILE_NAME          	= 'FILE_NAME_TO_BE_COPIED'

		# Create s3 bucket connection
		
		s3 = boto3.resource( 's3', aws_access_key_id=AWS_KEY, aws_secret_access_key=AWS_ACCOUNT_SECRET )
		s3_bucket = s3.Bucket( S3_BUCKET_NAME ) 

		# connect to oss
		oss_auth = oss2.Auth( OSS_KEY, OSS_SECRET )
		oss_bucket = oss2.Bucket( oss_auth, OSS_ENDPOINT, OSS_BUCKET_NAME )

		tmp_file = '/tmp/%s' % S3_BUCKET_NAME
		
		s3_bucket.download_file( FILE_NAME, tmp_file )

		with open(oss2.to_unicode(tmp_file), 'rb') as f:
			oss_bucket.put_object(FILE_NAME, f)
		print 'Key uploaded to oss.'
