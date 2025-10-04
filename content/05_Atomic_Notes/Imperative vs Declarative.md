## Choosing the Right Approach

| Approach    | Ideal Use Case                                               | Example Commands                                                                                                                    |
| ----------- | ------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------- |
| Imperative  | Quick, one-off tasks such as creating a pod or deployment.   | `kubectl run --image=nginx nginx`<br>`kubectl create deployment --image=nginx nginx`<br>`kubectl expose deployment nginx --port 80` |
| Declarative | Long-term management with version-controlled infrastructure. | `kubectl apply -f nginx.yaml`<br>`kubectl apply -f /path/to/config-files`                                                           |
