
# Various Kubernetes Snipplets

## Cleanup failed pods(oneliner)
	kubectl get pods -A | \
	  grep -E 'ImagePullBackOff|ErrImagePull|Evicted|Error|Completed|NodeAffinity' | \
	  awk '{print $2 " --namespace=" $1}' | \
	  xargs kubectl delete pod

## Fix Networking
Used this successfully in a case waher pods have been unable to communicate with each other saying "no route to host"

	systemctl stop docker
	iptables --flush
	iptables -tnat --flush
	systemctl start docker
