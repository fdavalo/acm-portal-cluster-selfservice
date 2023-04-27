## Use ACM as a Self Service Portal for creating/managing Openshift Clusters

 * As a pre-requisite, 
   * create the Cluster Role **infraenv**
   * modify the project template to add the **infraenv** Cluster Role as a Role Binding on all new Namespace created
     * cf https://docs.openshift.com/container-platform/4.12/applications/projects/configuring-project-creation.html
   * install operator GateKeeper (from tab operators) if you want to enforce naming and block pod creation
     
 * For each client that do you want to onboard on ACM for Cluster Self Service    
   * Create a Group (ex team01-users)
   * Create users and add them to the group (see example on manifests/groups.yaml ou users.yaml)
   * Create a ClusterSet and add a Cluster Role Binding to your group for the ClusterSet
   * The users should only see their clusters and create Infrastructure Environment to manage hosts to add/scale clusters
   
 * You can enforce namespace naming and block Pod creation for you client users
 * Namespace creation for your client will be used for naming clusters and infrastructure environment
   * see manifests in manifests/gatekeeper to apply those rules
   * check you have a label client-prefix in each Groups (see in manifests/groups.yaml)
   * apply gatekeeper.yaml, then config.yaml, then constrainttemplate.yaml and finally clientpolicy.yaml
     * each creation of a namespace by your client users will be block if the namespace is not prefixed by <the groupe prefix>-
        * ex namespace team3 won't be accepted for group team01-users
        * ex namespace team1-cluster1 will be accepted
        * ex namespace team1-infraenv1 will be accepted
     * on those namespaces, no Pod should be running as they only be used for configuration (secrets, manifestwork, ...)
