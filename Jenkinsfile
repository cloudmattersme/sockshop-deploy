node {

    stage('Checkout') {
		checkout scm
    }

    stage('Get Deployment YAML') {
        /* Let's make sure we have the repository cloned to our workspace */
        sh 'wget https://github.com/microservices-demo/microservices-demo/raw/master/deploy/kubernetes/complete-demo.yaml'
        sh "sed -i '/    nodePort: 30001/d' complete-demo.yaml"
    }

	stage('Prisma Cloud Scan') {
	    sh ''' api_response=`curl -s -H 'content-type: application/json'   https://scan.api.redlock.io/v1/iac   --data-binary "@complete-demo.yaml"`
         '''
    }

    stage('Deploy') {
		sh 'kubectl delete --ignore-not-found=true -f complete-demo.yaml'
   		sh 'kubectl apply -f complete-demo.yaml'
        sh 'kubectl patch deployment -n sock-shop carts-db --patch "$(cat carts-db.yml)"'
        sh 'kubectl patch deployment -n sock-shop orders-db --patch "$(cat orders-db.yml)"'
    }
}