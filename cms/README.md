# cost_model_swarm
docker swarm version for cost model


## Build the Cluster 
### ** Use Cloudlab Project Profile `4N` - wzheng, choose APT cluster

### Docker swarm init


#### For each node

```
sudo usermod -aG docker $USER
exit
pip3 install pandas==0.23.0
```


#### Master node

1. get IP 

```
dig +short myip.opendns.com @resolver1.opendns.com
```

2. init master

```
docker swarm init --advertise-addr <IP>
```

#### Worker node

use the command that given by inti master node to join the cluster



### Make sure no job running on the master node

1. get the ID for master node

```
docker node ls 
```

2. update the availability

```
docker node update --availability drain <ID> 
```





## Run Experiments

### ** Make sure run the algo(or no algo) first then submit jobs



### Run algo

#### worker node

To prevent the cloud offline, and to better check the status of running jobs, please use running the code inside `tmux`:

> open a tmux window: `tmux`
>
> detach the tmux: `ctl + b` then press `d`
>
> attach the tmux again: `tmux attach`



```
cd cost_model_swarm
python3 worker.py <joblist.csv> -i <interval to call the algo - default 10s > -m <master's ip>
```

>eg. `python3 worker.py test_joblist.csv -m 128.110.154.231 `



### Run no algo

#### worker node

To prevent the cloud offline, and to better check the status of running jobs, please use running the code inside `tmux`:

> open a tmux window: `tmux`
>
> detach the tmux: `ctl + b` then press `d`
>
> attach the tmux again: `tmux attach`



```
cd cost_model_swarm
python3 worker.py <joblist.csv> -i <interval to call the algo - default 10s > --no_algo
```

>eg. `python3 worker.py test_joblist.csv -i 10 --no_algo ` 



### Submit jobs

#### Master node

motify the `.csv` file to change the jobs

```
cd cost_model_swarm
bash rm_services.sh
python3 submit_jobs.py <joblist.csv>
```

>eg. `python3 submit_jobs.py test_joblist.csv `



#### Worker nodes

after master node submitted the jobs, we can check the states of the jobs on the worker node:

```
docker stats
```



### Stop algo / no_algo and collect data

after all jobs finished, we can stop the running algo/no_algo and collect data:

```
bash stop.sh
```

If you run the **algo**, the data will be stored inside `data` folder under `cost_model_swarm`.

If you run the **no_algo**, the data will be stored inside `no_algo` folder under `cost_model_swarm`.

