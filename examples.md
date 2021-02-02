1.  Enable Istio in Namespace
2.  Book info app on OKD - scale up application
    1. anyuid
    2. NetworkAttachmentDefinition
        1.oc adm policy add-scc-to-group anyuid system:serviceaccounts:NAMESPACE -> dedicated scc
          oc create ns NAMESPACE || true
          oc label namespace NAMESPACE istio-injection=enabled
          oc apply  -n NAMESPACE -f bookinfo/platform/kube/bookinfo.yaml
          oc -n NAMESPACE apply -f NetworkAttachmentDefinition.yaml
          oc -n NAMESPACE apply -f bookinfo/networking/destination-rule-all.yaml
    3.
    ```
    export INGRESS_HOST=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
    export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].port}')
    export SECURE_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="https")].port}')
    export TCP_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="tcp")].port}')
    export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT
    ```
3. Observability - istioctl dashboard
    1. Kiali
    2. Prometheus
    3. Jaeger
    4. Grafana
4. Deploy traffic shifting
    1. bookinfo/networking/virtual-service-all-v1.yaml
    2. bookinfo/networking/virtual-service-reviews-50-v3.yaml
    if stable
    3. bookinfo/networking/virtual-service-reviews-v3.yaml
5. Req routing based on identity
    1. virtual-service-all-v1.yaml
    2. bookinfo/networking/virtual-service-reviews-test-v2.yaml
6. Fault injection based on identity
    1. bookinfo/networking/virtual-service-all-v1.yaml
    2. bookinfo/networking/virtual-service-reviews-test-v2.yaml
    3. bookinfo/networking/virtual-service-ratings-test-delay.yaml
    4. failure
    5. bookinfo/networking/virtual-service-ratings-test-abort.yaml
7. Mirroring
    1. Mirroring/httpbin{}.yaml
    2. mirroring/virtualservice-destinationrule.yaml
    3. gen traffic, check logs
8. ingress - gateway
9. TLS
    1. peerauthentication
    2. bookinfo/networking/destination-rule-all-mtls.yaml
    3. bookinfo/networking/PeerAuthentication.yaml
10. Access logs  
    1. oc -n bookinfo2 logs reviews-v1-987d495c-mmlp9 -c istio-proxy   
