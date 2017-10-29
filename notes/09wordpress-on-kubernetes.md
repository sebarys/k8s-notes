# How set up Wordpress on Kubernetes

We will provide password for database by using secrets and Pod with wordpress and mysql containers which will be using this password.

Secrets deployment is in file *deployments/wordpress/wordpress-secrets.yml* and wordpress deployment you can find in *deployments/wordpress/wordpress-single-deployment-no-volume.yml*

After creating secrets and deployment from manifests you can check that your containers are using secrets by describing Pod.

To expose wordpress we need to create a service from file *deployments/wordpress/wordpress-service.yml*. After creating service we can acces to website by using node ip address and exposed port.

Keep in mind that all data collected in database will be lost on recreating Pods. To collect persistent data we need use Volumes.
