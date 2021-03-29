---
layout: post
author: Cagri
title: "Status Blog and Debugging Pman"
---

As you might remember from the last post, I built a local Openshift cluster with the CodeReady containers and deployed ChRIS services `pfioh` and `pman`. I continued testing our services with running a plugin with our `pman`. `pman` stands for Process Manager, and it manages the jobs by executing them. When I was running plugins, I was getting an error indicating that there's an error with the specifications of the container environment. For poeple who don't know, every service and application we write, we containerize them by creating an image. Every container has some specifications like `cpu limit`, `memory limit`, `number of workers` etc... This was the error I was getting:

```
(Thread-17 ) response body: {"kind":"Status","apiVersion":"v1","metadata":{},"status":"Failure","message":"Job in version \"v1\" cannot be handled as a Job: v1.Job.Spec: v1.JobSpec.Parallelism: readUint32: unexpected character: \ufffd, error found in #10 byte of ...|lelism\": \"1\", \"compl|..., bigger context ...|: {\"ttlSecondsAfterFinished\": 20, \"parallelism\": \"1\", \"completions\": \"1\", \"activeDeadlineSeconds\": 3|...","reason":"BadRequest","code":400}
Exception in thread Thread-17:
Traceback (most recent call last):
  File "/usr/lib/python3.8/threading.py", line 932, in _bootstrap_inner
    self.run()
  File "/usr/lib/python3.8/threading.py", line 870, in run
    self._target(*self._args, **self._kwargs)
  File "/usr/local/lib/python3.8/dist-packages/pman/pman.py", line 2037, in t_run_process_openshift
    self.get_openshift_manager().schedule(str_targetImage, str_cmdLine, self.jid,
  File "/usr/local/lib/python3.8/dist-packages/pman/openshiftmgr.py", line 276, in schedule
    job = self.kube_v1_batch_client.create_namespaced_job(namespace=self.project, body=d_job)
  File "/usr/local/lib/python3.8/dist-packages/kubernetes/client/api/batch_v1_api.py", line 58, in create_namespaced_job
    (data) = self.create_namespaced_job_with_http_info(namespace, body, **kwargs)  # noqa: E501
  File "/usr/local/lib/python3.8/dist-packages/kubernetes/client/api/batch_v1_api.py", line 135, in create_namespaced_job_with_http_info
    return self.api_client.call_api(
  File "/usr/local/lib/python3.8/dist-packages/kubernetes/client/api_client.py", line 340, in call_api
    return self.__call_api(resource_path, method,
  File "/usr/local/lib/python3.8/dist-packages/kubernetes/client/api_client.py", line 172, in __call_api
    response_data = self.request(
  File "/usr/local/lib/python3.8/dist-packages/kubernetes/client/api_client.py", line 382, in request
    return self.rest_client.POST(url,
  File "/usr/local/lib/python3.8/dist-packages/kubernetes/client/rest.py", line 272, in POST
    return self.request("POST", url,
  File "/usr/local/lib/python3.8/dist-packages/kubernetes/client/rest.py", line 231, in request
    raise ApiException(http_resp=r)
kubernetes.client.rest.ApiException: (400)
Reason: Bad Request
```

This clearly indicates that one of the parameters were supposed to be integer however it was reading `\ufffd` which is a replacement character in unicode. I looked into the template that we create the containers and started doing some experiments. This is one of the methods of our `openshiftmgr.py` that schedules the the jobs and their template:
```
    def schedule(self, image, command, name, number_of_workers, 
                 cpu_limit, memory_limit, gpu_limit, incoming_dir, outgoing_dir):
        """
        Schedule a new job and returns the job object.
        """
        d_job = {
            "apiVersion": "batch/v1",
            "kind": "Job",
            "metadata": {
                "name": name
            },
            "spec": {
                "ttlSecondsAfterFinished": 20,
                "parallelism": number_of_workers,
                "completions": number_of_workers,
                "activeDeadlineSeconds": 3600,
                "template": {
                    "metadata": {
                         "labels":{
                            "job-origin":"pman"
                        },
                        "name": name
                    },
                    "spec": {
                        "restartPolicy": "Never",
                        "containers": [
                            {
                                "env": [
                                    {
                                        "name": "NUMBER_OF_WORKERS",
                                        "value": number_of_workers
                                    },
                                    {
                                        "name": "CPU_LIMIT",
                                        "value": cpu_limit
                                    },
                                    {
                                        "name": "MEMORY_LIMIT",
                                        "value": memory_limit
                                    }
                                ],
                                ....
```

The most important part of this template is the `parallelism` , `completions` that are in the `spec` section and the value of `NUMBER_OF_WORKERS` in the `containers env`. As it seems, Even though the MOC environment can differentiate and Parse this JSON template without any problems, the local CodeReady Containers Openshift environment couldn't parse this JSON as it is and it threw and error. As you can see from my whiteboard while the 'parallelism' and 'completions' expects an integer, the 'NUMBER_OF_WORKERS' in the `containers env` expects a string. I found out about this by hardcoding some values and creating different images.

![File_000](https://user-images.githubusercontent.com/55101879/112177775-99d07a00-8bcf-11eb-879b-1b55daa46a29.jpeg)

I modified our `openshiftmgr.py` by changing the values that were read to integers. Here is a little snippet:

```
    def schedule(self, image, command, name, number_of_workers, 
                 cpu_limit, memory_limit, gpu_limit, incoming_dir, outgoing_dir):
        """
        Schedule a new job and returns the job object.
        """
        d_job = {
            "apiVersion": "batch/v1",
            "kind": "Job",
            "metadata": {
                "name": name
            },
            "spec": {
                "ttlSecondsAfterFinished": 20,
                "parallelism": int(number_of_workers),
                "completions": int(number_of_workers),
                ......
```

After this change I've tested this image both on MOC and on my local openshift and it worked perfectly. On the other hand, my mentor Mo Duffy wanted me to create a status blog about my work and suggested GitHub Pages and Jekyll. Hence I started watching some videos about Jekyll and started working on my blog which is this blog 😄. I'm wrapping up this blog post by saying that I think 70% of my work was debugging in these last two weeks. As always, Thansk for reading 😁
