Helm Chart: provision SQS/SNS/S3 resources with ACK


1. $ minikube start
2. $ helm install aws-ack --generate-name

3. The commands above download the latest version of the S3 controller. To select a different version,
change the RELEASE_VERSION variable and execute the commands again.
 $ export HELM_EXPERIMENTAL_OCI=1
 $ export SERVICE=s3
 $ export RELEASE_VERSION=`curl -sL https://api.github.com/repos/aws-controllers-k8s/s3-controller/releases/latest | grep '"tag_name":' | cut -d'"' -f4`
 $ export CHART_EXPORT_PATH=/tmp/chart
 $ export CHART_REF=$SERVICE-chart
 $ export CHART_REPO=public.ecr.aws/aws-controllers-k8s/$CHART_REF
 $ export CHART_PACKAGE=$CHART_REF-$RELEASE_VERSION.tgz

 $ mkdir -p $CHART_EXPORT_PATH

 $ helm pull oci://$CHART_REPO --version $RELEASE_VERSION -d $CHART_EXPORT_PATH
 $ tar xvf $CHART_EXPORT_PATH/$CHART_PACKAGE -C $CHART_EXPORT_PATH

 4. Once the Helm chart is downloaded and exported, you can install a particular ACK service controller using the helm install command:
 $ export ACK_K8S_NAMESPACE=ack-system
 $ export AWS_REGION=eu-central-1

 $ helm install --create-namespace --namespace $ACK_K8S_NAMESPACE ack-$SERVICE-controller \
    --set aws.region="$AWS_REGION" \
    $CHART_EXPORT_PATH/$SERVICE-chart

5. To verify that the Helm chart was installed, use the helm list command:

 $ helm list --namespace $ACK_K8S_NAMESPACE -o yaml