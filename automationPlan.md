#Private Automation#

###Purpose
Move our automation and tools outside of public repos.  Decouple development from deployments and provisioning.


###What's Migrating?
1. Docker scripts
    - Anything in the V2/docker directory
2. Tools (clusterCommander)
    - Anything in the V2/tools directory
3. Dependencies scripts
    - installDependencies.sh

###Migrating Where?
Private github repo.  Still needs to be created (DemandCube is currently maxed out on private repos)

###What needs to happen?
1. Docker scripts
    - scribengin.sh needs to be reworked since it depends on being in the same directory structure as Scribengin
    - All the release and deploy actions need to be reworked and tested to account for being in a different directory structure
2. Tools
    - A simple move to the new location should suffice I believe, the python code is independent of everything else
3. Dependecies script
    - Cleaned up - currently installs Queuengin and Commons to your cwd, so improve on that
4. Should we also move ansible-flow here?
    
###Migration Requirements
1. All automation scripts should be able to run from any directory
    - Example:
        -    ``` ./script.sh --option ```
    - Should execute successfully, and so should
        -    ``` /full/path/to/script --option ```
2. Automation and integration testing jobs on Jenkins need to be updated
    - Jobs for rebuilding docker images
    - Integration test jobs
3. Clear documentation for how to get set up with the new repo
4. Change Dockerfile so that the installation and configuration is handled by ansible


###New Work
1. Provisioning with ansible playbooks
    - Currently, our hadoop-worker container is configured exactly the same as our kafka container (for example)
    - Moving forward, each node in our cluster should only have the components it requires
    - Example: Kafka nodes only need Kafka installed, and should not have any other component, such as Zookeeper or Hadoop, installed on it
2. Moving our provisioning to ansible will allow us to reuse this code to provision not just our Docker images, but also on a real deployment
    - Will need to change docker.sh to reflect these changes
    - All configuration of containers must be removed from the Dockerfile and maintained in ansible playbooks
3. Ansible
    - playbooks
        - playbooks for common components (java, etc), kafka, hadoop-master, hadoop-worker, zookeeper, and provisioning scriengin components
    - roles
        - Separate into folders - dev and possibly production to mirror odyssey deployments.  Will make publishing playbooks for clients easier
        - Need to separate components as much as possible and maintain that separation
    - lib
        - Import ansible-flow into this repo
        


###New Repo Structure
```
/NeverwinterAutomation
    /ansible
        /lib
            ansible-flow #https://github.com/DemandCube/ansible-flow
        /playbooks
            /dev    #dev is here in case we want separate playbooks for dev vs. something like production/customers
        /roles
    /docker
        /scribengin
          Dockerfile
          docker.sh
    /tools
        /clusterCommander
        /scribengin
            scribengin.sh
            #Need tools here to do tasks like build, release scribengin
    /tests
        /scribengin
            /integration-tests

```
