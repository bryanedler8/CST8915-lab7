# 1. Port forward (keep this running)
kubectl port-forward service/rabbitmq 15672:15672

# 2. In another terminal, check queues (initially empty)
curl -u myuser:mypassword http://localhost:15672/api/queues

# 3. Create a queue via API
curl -u myuser:mypassword -X PUT http://localhost:15672/api/queues/%2F/demo-queue -H "Content-Type: application/json" -d '{"durable":true}'

# 4. Publish a message
curl -u myuser:mypassword -X POST http://localhost:15672/api/exchanges/%2F/amq.default/publish -H "Content-Type: application/json" -d '{"routing_key":"demo-queue","payload":"test message"}'

# 5. Verify queue has message
curl -u myuser:mypassword http://localhost:15672/api/queues

# 6. Delete pod and see data loss
kubectl delete pod $(kubectl get pods | grep rabbitmq | awk '{print $1}')
sleep 45
curl -u myuser:mypassword http://localhost:15672/api/queues
