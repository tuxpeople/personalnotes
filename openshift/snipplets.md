# Various OpenShift Snipplets

## AnyUID on Service Account

Problem:

    Warning  FailedCreate      4m22s (x564 over 2d6h)  replicaset-controller  Error creating: pods "minio-redacted-7c745b5bb5-" is forbidden: unable to validate against any security context constraint: [provider restricted: .spec.securityContext.fsGroup: Invalid value: []int64{1000}: 1000 is not an allowed group spec.containers[0].securityContext.runAsUser: Invalid value: 1000: must be in the ranges: [1000730000, 1000739999]]

Solution:

    $ oc project minio-redacted
    Already on project "minio-redacted" on server "https://my.awesome.openshift.io:6443".
    $ oc get sa
    NAME                                     SECRETS   AGE
    builder                                  2         22d
    default                                  2         22d
    deployer                                 2         22d
    minio-redacted                            2         22d
    minio-redacted-update-prometheus-secret   2         22d
    oc adm policy add-scc-to-user anyuid system:serviceaccount:minio-redacted:minio-redacted
    oc scale deployment minio-redacted --replicas=0
    oc scale deployment minio-redacted --replicas=1
