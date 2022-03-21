# scheduler_custom_metric


'''
flowchart LR
A[Cloud Scheduler] --> B(Cloud Function)
B --> C(Query BQ and Write Custom Metrics)
'''


## Steps

1. deploy private cloud functions:
```
gcloud functions deploy func_game_metrics \
  --source=/home/cliu/python/cloudmonitor/cloudfunction \
  --trigger-http \
  --region us-central1 \
  --runtime python39 \
  --service-account cliu201-sa@cliu201.iam.gserviceaccount.com \
  --no-allow-unauthenticated --timeout 540s 
```

2. create scheduler to invoke  cloud functions
```
gcloud scheduler jobs create http game-metrics-hourly-job \
  --description "ingest custom metric hourly" \
  --schedule "0 * * * *" \
  --time-zone "Asia/Shanghai" \
  --uri "https://us-central1-cliu201.cloudfunctions.net/func_game_metrics" \
  --http-method POST \
  --oidc-service-account-email=cliu201-sa@cliu201.iam.gserviceaccount.com
```