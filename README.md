# Deploy App To Docker Swarm

## Build images (Dockerfile)

### Nginx 

### Php-fpm

### MySql

## Docker compose

## Docker machine & Docker swarm
- Create machine
```
    # Create a VM (Mac, Win7, Linux)
    docker-machine create --driver virtualbox myvm1
    
    # Win10
    docker-machine create -d hyperv --hyperv-virtual-switch "DockerSwarmSwitch" manager
    
    # View basic information about your node
    docker-machine env myvm1                
    
    # List the nodes in your swarm
    docker-machine ssh myvm1 "docker node ls"         
    
    # Inspect a node
    docker-machine ssh myvm1 "docker node inspect <node ID>"   
```

- Docker swarm and deploy an app to swarm
```    
    # Create a swarm
    
    docker swarm init --advertise-addr <MANAGER-IP>
    
    # View join token
    docker-machine ssh myvm1 "docker swarm join-token -q worker"   
    
    # Open an SSH session with the VM; type "exit" to end
    docker-machine ssh myvm1   
    
    # View nodes in swarm (while logged on to manager)
    docker node ls                
    
    # Make the worker leave the swarm
    docker-machine ssh myvm2 "docker swarm leave"  
    
    # Make master leave, kill swarm
    docker-machine ssh myvm1 "docker swarm leave -f" 
    
    # list VMs, asterisk shows which VM this shell is talking to
    docker-machine ls 
    
    # Start a VM that is currently not running
    docker-machine start myvm1    

    # show environment variables and command for myvm1    
    docker-machine env myvm1      
    
    # Mac command to connect shell to myvm1
    eval $(docker-machine env myvm1)         
    
    # Windows command to connect shell to myvm1
    & "C:\Program Files\Docker\Docker\Resources\bin\docker-machine.exe" env myvm1 | Invoke-Expression   
    
    # Deploy an app; command shell must be set to talk to manager (myvm1), uses local Compose file
    docker stack deploy -c <file> <app>  
    
    # Copy file to node's home dir (only required if you use ssh to connect to manager and deploy the app)
    docker-machine scp docker-compose.yml myvm1:~ 
    
    # Deploy an app using ssh (you must have first copied the Compose file to myvm1)
    docker-machine ssh myvm1 "docker stack deploy -c <file> <app>"   
    
    # Disconnect shell from VMs, use native docker
    eval $(docker-machine env -u)     
    
    # Stop all running VMs
    docker-machine stop $(docker-machine ls -q)               
    
    # Delete all VMs and their disk images
    docker-machine rm $(docker-machine ls -q) 
```
