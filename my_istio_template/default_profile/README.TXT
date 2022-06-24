The instructor is creating the 1-istio-init.yaml and 2-istio-minikube.yaml by doing these steps:

1. Istioctl profile dump default > istio_default.yaml -> for default or use demo for the demo profile. The yaml file is an istio yaml, not a regular Kubernetes yaml.
2. From there, he generated the k8s yaml by applying this command:istioctl manifest generate -f <istio_config_filename>The istio_config_filename was generated from the Istioctl profile dump demo to generate an istio yaml file.This file will have all the custom resource definition as well, and it needs to be executed first when we need to apply if using kubectl.
3. He separated the custom resource definition from the yaml file generated before and put it in the 1-istio-init.yaml
4. Add namespace to the 1-istio-init.yamlapiVersion: v1
	kind: Namespace
	metadata:
  		name: istio-system

5. The rest is put to 2-istio-minikube.yaml
6. Apply the yaml using kubectl applykubectl apply -f ./1-istio-init.yamlkubectl apply -f ./2-istio-minikube.yaml
7. You can get the addons (Kiali, Grafana, Jaeger, Prometheus) from two places:
    1. https://github.com/istio/istio/tree/master/samples/addons
    2. Istio folder after you installed it locally. It is located under <istio_home>/samples/addons
8. You need to change the Service for each yaml, so it uses NodePort and enable the port in order to get them accessible from your browser.
The reason he wants this k8s yaml file instead of the istioctl install, so he doesn’t need to install istioctl. Everything can be automated and done through kubectl and the yaml file can be stored in the code repo.
This will create a repeatability to the system.

This is what I did for each add-on yaml to enable the port, so my browser can access it:
Grafana.yaml (Screenshot on apple note)
in the Service section:
spec:
  type: NodePort # Use NodePort
  ports:
    - name: service
      port: 3000
      protocol: TCP
      targetPort: 3000
      nodePort: 31103 # Enable the external port
Jaeger.yaml(Screenshot on apple note)
Similar to what I did in Grafana above.

Kiali.yaml(Screenshot on apple note)
Similar to what I did in Grafana above.

We need to think about how to enable this in the real world environment. We may not want to expose it to the outside world.
That’s why Anthos Service Mesh doesn’t use this, and uses their own tooling that is protected.
