NS=doclab

.PHONY: up down apply logs watch clean

up:
	kind create cluster --config kind-cluster.yaml
	kubectl create ns $(NS) || true
	kubectl config set-context --current --namespace=$(NS)
	kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
	kubectl -n ingress-nginx rollout status deploy/ingress-nginx-controller

apply:
	kubectl apply -f manifests/config.yaml
	kubectl apply -f manifests/app.yaml
	kubectl apply -f manifests/job.yaml
	kubectl apply -f manifests/cronjob.yaml
	-kubectl apply -f manifests/ingress.yaml

logs:
	kubectl logs deploy/echo-deploy -f

watch:
	kubectl get pods,svc,deploy,job,cronjob -w

down:
	kind delete cluster --name doc-demo

clean: down