These servers are virtual, called EC2. Amazon Elastic Compute Cloud. 

Imagine if we had to do everything ourselves, that would cost a lot, therefore, having AWS do all the hard parts for us. 
The worst part of doing it ourselves, is that if we have too many, then we just have too many, and then we don't really use them. 
Like if we had a business, that did a lot in Christmas, but not the rest of the year, we would have to buy gear for the busy Christmas period, otherwise that would fail, and then you just have too many servers for the rest of it. 

Then, remember, we only pay for what we use. 
##### Virtualization
EC2 runs on top of physical host machines that are managed by AWS using virtualization tech. 
We aren't taking a whole host machine when we spin one up, we are just taking part of it. The host is shared by a bunch of these instances, AKA virtual machines. 
Hypervisors - run and make sure everything is sharing the resources between all the instances. 
Sharing underlying hardware is called multitenancy (multiple tenants on the same machine). 
Hypervisor will isolate the VMs from each other as they share resources from the host. 
Though they may be sharing resources, they are secure, and aren't aware of each other. 

We can choose the OS on the instance. 

Then we can configure what we want running on the instance. 

They are resizable, meaning we can start them off small, then give them more juice, the more stuff we want them to run. This is known as **vertically scaling**. 

We can also control the networking of instances, making them public or private? 


- They are fast to set up. 
- Stop using, and therefore stop paying, whenever we like. 
- Set up more, very quickly, vertically scale depending on your needs .


### Launch
First, launch an instance. 
Select the template with basic configurations: OS, application server, or applications. 
Then instance type: specific hardware config etc. 
Then we set the security settings, what can flow in an out of your instance. 

### Connect
Then we need to connect to the instance: there are several ways to do that. 
Programs and apps have ways of connecting directly to the instance and exchange data. 
Or the user can log in and access the computer desktop. 

### Use
After connecting, you can being using it. 
Run commands to install software, add storage, copy and organize files and more. 

## Instance Types
Businesses have a variety of tasks that they need doing. 
The instance has to be well suited for what the business needs, therefore there are different types. 

There are instance families, that are optimized for certain types of tasks. 
There are varying combinations of CPU, memory, storage, and networking capacity, and give you the flexibility to choose the appropriate mix of resources for the apps that a business needs. 
General purpose, compute optimized, memory optimize , accelerated computing, and storage optimized. 

General purpose instances provide a good balance of compute, memory, and networking resources, and can be used for a variety of diverse workloads like web service or code repositories.

Compute optimized instances are ideal for compute-intensive tasks like **gaming servers**, high performance computing or HPC, and even scientific modelling.

Similarly, memory optimized instances are good for memory-intensive tasks. Accelerated computing are good for floating point number calculations, graphics processing, or data pattern matching, as they use hardware accelerators.

##### General purpose instances

**General purpose instances** provide a balance of compute, memory, and networking resources. You can use them for a variety of workloads, such as:

- application servers
- gaming servers
- backend servers for enterprise applications
- small and medium databases

Suppose that you have an application in which the resource needs for compute, memory, and networking are roughly equivalent. You might consider running it on a general purpose instance because the application does not require optimization in any single resource area.

##### Compute optimized instances

**Compute optimized instances** are ideal for compute-bound applications that benefit from high-performance processors. Like general purpose instances, you can use compute optimized instances for workloads such as web, application, and gaming servers.

However, the difference is compute optimized applications are ideal for high-performance web servers, compute-intensive applications servers, and dedicated gaming servers. You can also use compute optimized instances for batch processing workloads that require processing many transactions in a single group.

##### Memory optimized instances

**Memory optimized instances** are designed to deliver fast performance for workloads that process large datasets in memory. In computing, memory is a temporary storage area. It holds all the data and instructions that a central processing unit (CPU) needs to be able to complete actions. Before a computer program or application is able to run, it is loaded from storage into memory. This preloading process gives the CPU direct access to the computer program.

Suppose that you have a workload that requires large amounts of data to be preloaded before running an application. This scenario might be a high-performance database or a workload that involves performing real-time processing of a large amount of unstructured data. In these types of use cases, consider using a memory optimized instance. Memory optimized instances enable you to run workloads with high memory needs and receive great performance.
##### Accelerated computing instances

**Accelerated computing instances** use hardware accelerators, or coprocessors, to perform some functions more efficiently than is possible in software running on CPUs. Examples of these functions include floating-point number calculations, graphics processing, and data pattern matching.

In computing, a hardware accelerator is a component that can expedite data processing. Accelerated computing instances are ideal for workloads such as graphics applications, game streaming, and application streaming.

[](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/)

1. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/hYbNmTuR2nrAR-LopEsfpykauIM2Dq4X)
    
2. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/0O2vgW7odTp0UvGreyFgcR60yxVhq0on)
    
3. 1. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/ke7izqDhzqH2OUM_K8_9rpxGoCesJ7BW)
        
    2. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/cL3cgD5WO68DDtrOlI9vdKLgh5Q83aH5)
        
    3. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/VsgjIQDObQ6pVGaHabKwGbNiRAeWLyvl)
        
4. 1. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/B9dogMytxSqAqroCLNr_gJLxqik9t1G_)
        
    2. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/Otl_wKl2O-aaGLB1gDa6p-GPuDTh-rJ2)
        
    3. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/hnB0Adke2lqWBMvnYcWyVRw-65R88FLh)
        
    4. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/963zVDLD-RNO8U4MVY8ySiMVMwBlAUSl)
        
    5. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/fGclqAz5wpEwGJob9OrTFDG9e1giJXXn)
        
    6. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/bbA80JX5XCvydUwu_lIIAJXxCcoE37Ws)
        
    7. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/88pLbeCL6TKSk3DkGRRLTd8oNv7UU_f-)
        
    8. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/KAEkxYIkuo0dSC9VVjKw8wHg1UR_QC-0)
        
    9. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/RDirovVuVi2oTq48H5Vlsy155QS04RST)
        
5. 1. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/nZNkm9scMmVzmt2jn5ghcj6JELCylI6m)
        
    2. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/ZKRzn9jMEVQPU1X8jYM3XSIhYW8_fgBh)
        
    3. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/CP7UzXIwB18VNOcefcs9YfD-BFZT8CEa)
        
    4. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/So_pWcqPD6C6djUA28By7Xz0BXWxDqyQ)
        
    5. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/L3fk6Fnn4-JSHm9oxEnNltN9_NjeRgcE)
        
    6. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/Q8P6vSQ-mGlFiJ2O2JdTH7o2Rt41LFYs)
        
6. 1. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/0Lq8b6fpBOEu4r7vSUNmiBKukJddT8DW)
        
    2. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/X4CJ9IdVm1RwCJdg4MH6F6wl0U7EOI24)
        
    3. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/XiPZXeCaacPr21Hd57dm462ec1MSskIu)
        
    4. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/mZcuZTaRRs8EhGyIwjqhmk8Dv95IPsip)
        
    5. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/H8RtDI83qM7PtGmdUyEzO0xK_3HH6m-L)
        
    6. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/4oKtFbYEBx0Mu3ESxobXN8Kzfdn22se8)
        
7. 1. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/adOpoYSbKc9-FwXQ2SlHLr5H6PLwNBy7)
        
    2. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/d8Vjx4xMHntmTwYlGWBnqxGDXYxYeLOF)
        
    3. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/ypcjr0gEsDZerLZPnSGN-bIgeNa67Irt)
        
    4. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/rjD2DqrgNrnVlueqkpN9mYwL5SCjfD8S)
        
    5. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/c3_-80i6PXxKZmcAve2ggiwKckDIg9DZ)
        
    6. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/hxY6VEkS67O6uzrmmjD5QJ7wT4_RAqVA)
        
    7. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/uiGoPirlaW1g0vIO385z1gYXNjdnw3B-)
        
    8. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/2N112JH7_x5qiSxnbMj8G6naIy2d5BkR)
        
    9. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/cZad2GKhiF5Z6nliJGep-qHN6GkSdBHD)
        
    10. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/gEtcU-w56lXbNzh-IBYFHECTQwznOA4u)
        
    11. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/TVR0XwSTfulnsrGm8STuWFlSujC5Ehew)
        
8. 1. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/zyMTZP46Q5YEcHTlSH2KeK9wodGAYSCD)
        
    2. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/hu6esA_nuQkZ8rUadU1hF8YX79SKTu-W)
        
    3. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/ddqpKqEsMhIwhluaupqffvsNjgkstxQv)
        
    4. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/YgWjRhua1y27kz3vaKPA9Mj3-b2NxJQc)
        
    5. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/3MBIm1JDXjg1uvnaGJO08nCiuGwQH9sk)
        
    6. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/Npzl20Y25W2yYze8fhEaWRJ1FOxLAr6N)
        
    7. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/Ea8tptzpZSpUH06-qfc2i7DxFbwbYSRM)
        
    8. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/JbulHC_dqQmDvwVvevkiLdc7GP4GAmDU)
        
    9. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/CShw-2UhwLtOB6ym9zGuwF5kcTmy1CxX)
        
9. 1. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/pt6MOidLSVj5lIfWEQHIp4s6Q2ShFDn6)
        
    2. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/cu1d2bbX-emqLKA3POLMCZPESuJJbLUO)
        
    3. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/n2kduIUMFbtdwOdWDfW_cuc9ecpaiWS6)
        
    4. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/evJeNWgESM40HTDe7w90SN_3vkTSmXPW)
        
    5. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/qqCiAr9AFKCcwlvFucUEy9ghjySP2ew7)
        
    6. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/B-KrkicWdvVdlCdPywTDhViG2q98t-PC)
        
10. 1. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/vHQPnVsWEiuzFOfopm6Du5URTcFSjXYL)
        
    2. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/6RLnuRit2djFTLuMYpQXdnMVp2yYxdpE)
        
    3. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/btuDtj-kvR5u_1rqbBY8C2281Jq95Yti)
        
    4. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/79qAWpxmyqM-BUDEUZ5r8bEMvdA_j9wb)
        
    5. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/HJKm8Jk3ZtkIHV5pmBYKrBqyDpK1Tg04)
        
    6. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/GvwV7bLrjLAj2ekAPhVxH8g8metHZ6X8)
        
    7. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/vWhNtzH1PFQeu3ijJ_EBJp4P5BIBnhoL)
        
    8. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/FiIwLDFye2ZllgWgMbEp2NkOYuqOdL-R)
        
    9. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/FCaqS2vfoOsAfS8TELW7ETGrv4FZLPIO)
        
    10. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/jDji40KkwWsJzfeDeqCPGtwQ_VGL8pdO)
        
    11. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/AOjVQHD1wS8SQlvqDIV37NeCfEyN-Jxa)
        
11. 1. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/Nxs4MKPZfu-xgORUojonPX_uk1mm6P6-)
        
    2. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/ZipVimD40oA2wgLuktCYm7Y3tTLRAV4C)
        
    3. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/t3iijq7NDjlXnrrbTvEs6u84K-6A7u5D)
        
    4. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/ORd-QYistvILMQhg_Fh48jBTioaj1Vde)
        
    5. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/bw72PQ32P8bkfETeP6HJAQxaWi7gdo8b)
        
    6. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/kFhc_klhAngdAxeqTFNOujmwZT8Gz3np)
        
    7. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/n10Bh681Q-yQR-aPQwVQWIewEfq0pcXb)
        
12. 1. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/7bul06y3BFk9IUyAyF_cIxjdveGBQzpu)
        
    2. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/VeEjtWhXEoZHwUA0qrkEQCrewDCca8v7)
        
    3. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/1xeZ9ZAtqo2YyTCdJwosd2AgM81S_EVk)
        
    4. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/fOmII_if2nJxJHB-vcoYQ2Wy0P9scoa2)
        
    5. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/fDe3avUPwZWo8L9WpWfcjtbnfuM5Xqge)
        
13. 1. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/2HlZ--Ke6XFXBjmZCren1Mv6x3EOdkhT)
        
    2. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/yfEon5Q9Ssz3REK_zHcSzi-PJ6i-HodB)
        
    3. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/BlTYjzXqxBvAfZhyYFgImng2PrlJsBEn)
        
14. 1. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/M8CZyqR5EdDhXrYoX5kD6UELYKJou3-p)
        
15. 1. [](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/EKo3N8tlxeDfJWq7Yt8TijWzRHONF44a)
        

[Lesson 6 - Module 2 Introduction](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/index.html?enhanced_signature=b0SKx7DENg2yinEC3nRFqayzihGlFnEhxdrcs6RT558&endpoint=https%3A%2F%2Fexplore.skillbuilder.aws%2Flrs-api%2F&auth=Basic%20ZTUwMWE1OTktMjk4NS00MTJmLWFmYmMtYTgzMmRhM2UwYzg4OjMzMzE4NmNlN2E0NGE0NTM4Yzg1ZjQ4YmNkOTc2ZGRi&actor=%7B%22name%22%3A%22Hugo+Easlea%22%2C%22mbox%22%3A%22mailto%3Ahugoeaslea%40hotmail.co.uk%22%7D&registration=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&activity_id=http%3A%2F%2F4Q5nh7ZApg1D_nJmMdw7yYiLAX_s3gPY_rise&Accept-Language=en&course_id=134&content_token=2bec23c4-b7a8-4b50-aee0-8d8b68ea39a8&session_context=lms&host=kfase3bzbc.execute-api.us-east-1.amazonaws.com&path=/v1/xApi/&rs=679695b8e58e0&crct=82596c088b7d524536ba898e6789054b05b38dfb9d27dc589b6966ca379718ea9973ea27b9c97f7030820565ccc3df6b6493d65e172d3c6e406aed9feebffcff&course_code=TCAA-DIG-100-CECPEB-0104-EN-US&course_id=134&username=7f571d27-5e3a-480c-be61-5e641b3d71f8&user_id=6972610&hash=ea13334abe20c755395dcc836db8173bbcef2c04b09b35dfc610ad418548ad74#/lessons/B9dogMytxSqAqroCLNr_gJLxqik9t1G_)

Lesson content

# 

Amazon EC2 Instance Types

To start the video on Amazon EC2 instance types, choose the play button.

![](https://explore.skillbuilder.aws/files/a/w/aws_prod1_docebosaas_com/1737932400/qDULxVMdNi0QkaEhNOFWwg/tincan/fe470bc5add63f94f005d3da17a6db8131e78b9e/assets/T1C05-A_Little_More_About_EC2_V02_Final.png)

Play Video

To access a transcript of the video, choose the following transcript.

## 

Video transcript

Now that we've learned about EC2 instances and the crucial role they play in AWS, let's talk about the different types of EC2 instances that are available. Thinking back to our coffee shop analogy, you'll remember that EC2 instances are like our employees and that they serve client requests. If we want to have a cafe that can serve a lot of customers, then we're probably going to need multiple employees, right? And they all can't just be cashiers. We also need someone to make the drinks, someone to handle the food, and maybe someone to do that cool latte art that our customers love so much. Like any business, there are a variety of tasks that need to be done, and they often require different skillsets. 

  

If we want our business to operate as efficiently as possible, it's important to make sure that an employee's skillset suits their role. In the same way that our coffee shop has different kinds of employees, AWS has different types of EC2 instances that you can spin up and deploy into your AWS environment. 

  

Each instance type is grouped under an instance family and are optimized for certain types of tasks. Instance types offer varying combinations of CPU, memory, storage, and networking capacity, and give you the flexibility to choose the appropriate mix of resources for your applications. The different instance families in EC2 are general purpose, compute optimized, memory optimized, accelerated computing, and storage optimized. 

  

General purpose instances provide a good balance of compute, memory, and networking resources, and can be used for a variety of diverse workloads like web service or code repositories. 

  

Compute optimized instances are ideal for compute-intensive tasks like gaming servers, high performance computing or HPC, and even scientific modeling. 

  

Similarly, memory optimized instances are good for memory-intensive tasks. Accelerated computing are good for floating point number calculations, graphics processing, or data pattern matching, as they use hardware accelerators. 

  

And finally, storage optimized are good for, can you guess it? Workloads that require high performance for locally stored data. 

  

Now, if we map this back to our coffee shop, our cashier becomes a memory optimized EC2 instance, baristas become compute optimized instances, and our latte art employee is an accelerated computing instance type. And there you have it, EC2 instance types.

## 

**Amazon EC2 instance types**

[Amazon EC2 instance types(opens in a new tab)](https://aws.amazon.com/ec2/instance-types/) are optimized for different tasks. When selecting an instance type, consider the specific needs of your workloads and applications. This might include requirements for compute, memory, or storage capabilities.

To learn more about Amazon EC2 instance types, expand each of the following five categories.

## 

General purpose instances

**General purpose instances** provide a balance of compute, memory, and networking resources. You can use them for a variety of workloads, such as:

- application servers
- gaming servers
- backend servers for enterprise applications
- small and medium databases

Suppose that you have an application in which the resource needs for compute, memory, and networking are roughly equivalent. You might consider running it on a general purpose instance because the application does not require optimization in any single resource area.

## 

Compute optimized instances

**Compute optimized instances** are ideal for compute-bound applications that benefit from high-performance processors. Like general purpose instances, you can use compute optimized instances for workloads such as web, application, and gaming servers.

  

However, the difference is compute optimized applications are ideal for high-performance web servers, compute-intensive applications servers, and dedicated gaming servers. You can also use compute optimized instances for batch processing workloads that require processing many transactions in a single group.

## 

Memory optimized instances

**Memory optimized instances** are designed to deliver fast performance for workloads that process large datasets in memory. In computing, memory is a temporary storage area. It holds all the data and instructions that a central processing unit (CPU) needs to be able to complete actions. Before a computer program or application is able to run, it is loaded from storage into memory. This preloading process gives the CPU direct access to the computer program.

  

Suppose that you have a workload that requires large amounts of data to be preloaded before running an application. This scenario might be a high-performance database or a workload that involves performing real-time processing of a large amount of unstructured data. In these types of use cases, consider using a memory optimized instance. Memory optimized instances enable you to run workloads with high memory needs and receive great performance.

## 

Accelerated computing instances

**Accelerated computing instances** use hardware accelerators, or coprocessors, to perform some functions more efficiently than is possible in software running on CPUs. Examples of these functions include floating-point number calculations, graphics processing, and data pattern matching.

  

In computing, a hardware accelerator is a component that can expedite data processing. Accelerated computing instances are ideal for workloads such as graphics applications, game streaming, and application streaming.

###### Storage optimized instances

**Storage optimized instances** are designed for workloads that require high, sequential read and write access to large datasets on local storage. Examples of workloads suitable for storage optimized instances include distributed file systems, data warehousing applications, and high-frequency online transaction processing (OLTP) systems.

In computing, the term input/output operations per second (IOPS) is a metric that measures the performance of a storage device. It indicates how many different input or output operations a device can perform in one second. Storage optimized instances are designed to deliver tens of thousands of low-latency, random IOPS to applications. 

You can think of input operations as data put into a system, such as records entered into a database. An output operation is data generated by a server. An example of output might be the analytics performed on the records in a database. If you have an application that has a high IOPS requirement, a storage optimized instance can provide better performance over other instance types not optimized for this kind of use case.

## Billing Options

##### On-Demand
Only pay for the duration the instance funs for. Per Hour or Per Second. 
This is great for paying for stuff just to play around, get stuff going quickly. 
No long term, or upfront payments are needed. 

##### Saving Plan
In exchange for commitments to a certain amount of usage, there are lower prices. Measured in dollars per hour for a one or three-year term. There are savings of up to 72% with this model. 

##### Reserved Instances
Steady-state workloads or with predictable usage and offer you up to 75% discount versus on-demand

##### Spot Instances
Requesting spare Amazon EC2 computing capacity for up to 90% off the On-Demand Price. 
The catch being that they can reclaim that instance at any time they need it, giving a two minute warning to save work etc. 

##### Dedicated Hosts
Physical host dedication. Nobody else will share tenancy of these. 


## On-Demand
**On-Demand Instances** are ideal for short-term, irregular workloads that cannot be interrupted. No upfront costs or minimum contracts apply. The instances run continuously until you stop them, and you pay for only the compute time you use.  
  
Sample use cases for On-Demand Instances include developing and testing applications and running applications that have unpredictable usage patterns. On-Demand Instances are not recommended for workloads that last a year or longer because these workloads can experience greater cost savings using Reserved Instances.

## Reserved Instances

**Reserved Instances** are a billing discount applied to the use of On-Demand Instances in your account. There are two available types of Reserved Instances:

- Standard Reserved Instances
- Convertible Reserved Instances

You can purchase Standard Reserved and Convertible Reserved Instances for a 1-year or 3-year term. You realize greater cost savings with the 3-year option. 

**Standard Reserved Instances**: This option is a good fit if you know the EC2 instance type and size you need for your steady-state applications and in which AWS Region you plan to run them. Reserved Instances require you to state the following qualifications:

- **Instance type and size:** For example, m5.xlarge
- **Platform description (operating system):** For example, Microsoft Windows Server or Red Hat Enterprise Linux
- **Tenancy:** Default tenancy or dedicated tenancy

You have the option to specify an Availability Zone for your EC2 Reserved Instances. If you make this specification, you get EC2 capacity reservation. This ensures that your desired amount of EC2 instances will be available when you need them. 

**Convertible Reserved Instances**: If you need to run your EC2 instances in different Availability Zones or different instance types, then Convertible Reserved Instances might be right for you. Note: You trade in a deeper discount when you require flexibility to run your EC2 instances.

At the end of a Reserved Instance term, you can continue using the Amazon EC2 instance without interruption. However, you are charged On-Demand rates until you do one of the following:

- Terminate the instance.
- Purchase a new Reserved Instance that matches the instance attributes (instance family and size, Region, platform, and tenancy).

## EC2 Instance Savings Plans

AWS offers Savings Plans for a few compute services, including Amazon EC2. **EC2 Instance Savings Plans** reduce your EC2 instance costs when you make an hourly spend commitment to an instance family and Region for a 1-year or 3-year term. This term commitment results in savings of up to 72 percent compared to On-Demand rates. Any usage up to the commitment is charged at the discounted Savings Plans rate (for example, $10 per hour). Any usage beyond the commitment is charged at regular On-Demand rates.

The EC2 Instance Savings Plans are a good option if you need flexibility in your Amazon EC2 usage over the duration of the commitment term. You have the benefit of saving costs on running any EC2 instance within an EC2 instance family in a chosen Region (for example, M5 usage in N. Virginia) regardless of Availability Zone, instance size, OS, or tenancy. The savings with EC2 Instance Savings Plans are similar to the savings provided by Standard Reserved Instances.

Unlike Reserved Instances, however, you don't need to specify up front what EC2 instance type and size (for example, m5.xlarge), OS, and tenancy to get a discount. Further, you don't need to commit to a certain number of EC2 instances over a 1-year or 3-year term. Additionally, the EC2 Instance Savings Plans don't include an EC2 capacity reservation option.

Later in this course, you'll review AWS Cost Explorer, which you can use to visualize, understand, and manage your AWS costs and usage over time. If you're considering your options for Savings Plans, you can use AWS Cost Explorer to analyse your Amazon EC2 usage over the past 7, 30, or 60 days. AWS Cost Explorer also provides customized recommendations for Savings Plans. These recommendations estimate how much you could save on your monthly Amazon EC2 costs, based on previous Amazon EC2 usage and the hourly commitment amount in a 1-year or 3-year Savings Plan.

## Spot Instances

**Spot Instances** are ideal for workloads with flexible start and end times, or that can withstand interruptions. Spot Instances use unused Amazon EC2 computing capacity and offer you cost savings at up to 90% off of On-Demand prices.  
  
Suppose that you have a background processing job that can start and stop as needed (such as the data processing job for a customer survey). You want to start and stop the processing job without affecting the overall operations of your business. If you make a Spot request and Amazon EC2 capacity is available, your Spot Instance launches. However, if you make a Spot request and Amazon EC2 capacity is unavailable, the request is not successful until capacity becomes available. The unavailable capacity might delay the launch of your background processing job.  
  
After you have launched a Spot Instance, if capacity is no longer available or demand for Spot Instances increases, your instance may be interrupted. This might not pose any issues for your background processing job. However, in the earlier example of developing and testing applications, you would most likely want to avoid unexpected interruptions. Therefore, choose a different EC2 instance type that is ideal for those tasks.


## Dedicated Hosts

**Dedicated Hosts** are physical servers with Amazon EC2 instance capacity that is fully dedicated to your use. 

You can use your existing per-socket, per-core, or per-VM software licenses to help maintain license compliance. You can purchase On-Demand Dedicated Hosts and Dedicated Hosts Reservations. Of all the Amazon EC2 options that were covered, Dedicated Hosts are the most expensive.

# Scaling of EC2
Scalability and Elasticity: capacity can grow based on business. 
Customer workloads will vary over time: perhaps over the 24 hour period. 
The trouble is if we had on premises, then we have to know how bad the busy times are, buy for that, then tank the cost at the low turnover periods. 

 If one fails, then we have another that could work as well. 

#### Scalability
Starting with the resources that you need and designing your architecture to automatically respond to changing demand by scaling out or in. 
NO need to worry about lack of resources at peak time. a
What if we want to do this automatically : well then, we can have Amazon EC2 Auto Scaling. 

Just auto adding instances when we have longer queues. 

**Dynamic Scaling:** - Responding to changing demand. 

**Predictive Scaling:** - auto schedules the right number of Amazon EC2 instances based on predicted demand. 

Then you can use them together as well. 

##### More on Scale
Scale up or Scale out. 

Scaling up - adding more powers to the machines that are running, May make sense here or there, but sometimes the computing power is not going to do anything. 

Sometimes what we really need is more instances. 
Then we can add instances based on demand, or decommission them when we don't need them anymore. 

![[Pasted image 20250126233322.png]]
Will default to the minimum if not set a desired. 

Then we also have the maximum. 

# Directing Traffic With Elastic Load Balancing
Imagine in a coffee shop, then we have a lot of people coming in, however, they are stupid, as always. They only line up in the same line, despite, there potentially being more than one server. Then we need a way in order to get to the balance out the queues. 
That's why we need something like a host. Someone who will come in an rectify the situation, making sure that people go to the right line, balance it all out. 

How does the request know which EC2 to go to? To solve this, we need something called load balancing: 
It's an application that takes in requests and routes them to the instances to be processed. 
There are lots of the shelf. If you have a favourite flavour, then AWS will allow for that. 

##### Elastic Load Balancing
ELB, is a regional construct, more on this in later lessons, but it means that the service is highly available, and it's not just for individual instances. 

It's auto scaling. 
As traffic grows, ELB is designed to handle the additional throughput, with no change to the hourly cost. 
When we scale out, they just let the balancer know that they are there, and they are ready. 
When we scale in, ELB first stops all new traffic, waits for existing requests to complete, to drain out. 
Then the auto scaling engine can decommission the other instances. 

Imagine there are front end instances, and back end instances. Now imagine that there is a new back end instance. It would have to let all the front end instances know that it is ready to go. 
However, what if there are 1000's of these things. Then that is going to be really tough. 
The ELB is the mediator between them. It's just one URL that each front end instance uses. 
That will direct everything to the new stuff. 
This is what the decoupled nature of the architecture really means. 

#### Elastic Load Balancing
Auto distributing incoming application traffic across multiple resources, such as Amazon EC2 instances. 

The single point of contact, for our Auto Scaling group. 
![[Pasted image 20250126234656.png]]![[Pasted image 20250126234708.png

# Messaging and Queueing
Imagine back in the coffee shop, in order to get the message from the cashier to the barista, without having to do a ton of talking, we need some form of message. Imagine that the barista goes on break, then the cashier will have to queue up those messages, until he is back. 
Therefore we need a buffer, obviously a queue. 
Then applications, or even subsystems can send messages to each other. 
The idea of this, going through another system is a great sense of decoupling. 
They don't communicate immediately with each other, they go through another system. 
Tightly coupled systems, at any level, will cause issues. 
Messages are put into the queue from Application A: then processed by Application B. 
If Application B fails, then A will not experience any errors, it sent the message elsewhere, and it washed its hands clean of what happens to it. 
A can still messages over to the buffer. 

Loosely coupled, non-dependent instances, is what we want to have happen at the back end of AWS. 

**Amazon Simple Queue Service** or **SQS**. 
**Amazon Simple Notification Service**l or **SNS**. 

##### SQS
Send, store and receive messages between software component at any volume. 
Without losing messages or requiring other services to be available. 
Order board is our SQS queue. 
Messages have the person's name, coffee order, and the time they ordered. 
This data in the message is known as the payload, protected until delivery. 
Queues, are where they are stationed, until they are ready to move on. 
The queues will of course scale automatically, they are reliable, easy to configure and use. 


**SNS**
Is similar, used to send messages to services, but can also send to end users as well. 
In a different way called a publish/subscribe or pub/sub model. 
We can create an SNS topic which is just a channel for messages to be delivered. 
Then configure the subscribers to that topic and finally publish messages to a topic which will then fan out to all the subscribers in a single go.
These subs can be endpoints, such as SQS queues, AWS lambda Functions, and HTTPS or HTTP web hook. 

There can also be to end users, like using mobile push, SMS, and/or email. 
This could be a simple push notification when a customer's order is ready, or a mobile push. 

#### Monolithic Applications and Microservices
Apps are made of multiple components. 
They communicate with each other, transmit data, fulfil requests, and keep the application running. 
Suppose we have tightly coupled components: databases, servers, the UI, business logic, and so on. 
These can be considered **monolithic applications**. 
If a single component fails, then other components fail. 
Then possibly the entire thing fails. 

To maintain application availability when a single component fails, we can design through a **microservice** approach. 

There is a loose coupling here. 

If one component fails, the other components do not. 
They are communicating to each other, rather than being up each other's holes. 
When designing apps on AWS, we can take this microservice approach, with components that fulfil different functions. 
Two services for this are: 
**SNS** and **SQS**, they essentially allow the communication between the components, the microservices. 
SNS : Amazon's simple notification service. Publisher publishes messages to subscribers. 
Like the cashier provides coffee orders to the barista who makes the drinks. 

These subscribers can be a host of things, like web servers, email addresses, AWS Lambda Functions, or several other options. 

AWS Lambda Functions are code snippets that run in response to event, such as user actions or changes in state. It is event driven: 
**Event-Driven**: Lambda functions are triggered by events, such as:
- HTTP requests via API Gateway.
- File uploads to S3.
- Messages from SQS or SNS.
- Changes in DynamoDB tables

Remember that it's possible for subs to sub to more than one topic. 

### Amazon SQS: 
Message Queue Service: 
Send, store, receive messages between software components (micro services). 
A user or a service will receive (act) on those messages in the queue, does something based on it, then destroys the message. 

![[Pasted image 20250127133303.png]]

What will happen when the cashier takes an order, but the barista is not there. 
Perhaps the cashier should just wait until they can process the order, then hold up the queue etc. nah, just put it in the queue man. 

![[Pasted image 20250127133558.png]]The cashier at no point, need stop working. 
Then the barista just goes through FIFO. 

# Additional Compute Services
EC2 are virtual machines, minimal friction to get up and running. 
Running basic web services, to high performance computing clusters - everything in between, based on the instance that we are running. 

EC2 requires that we set up our fleet and manage them over time. 
The user is responsible for patching when new software comes out, setting up the scaling of those instances, ensuring you're architected your solutions to be hosted in a highly available manner. 
There is still some management that needs to be done here. 
That can be an issue for some people. 

What other services that are more convenient from a management perspective. 

The term serverless comes in here.
There are multiple serverless compute options. 
**Serverless** means that you cannot actually see or access the underlying infrastructure or instances that are hosting your application. 
Instead, every single thing is taken care of you. 
To the user, it just works, and it looks serverless. It works but to the user it's almost like they don't have a server. 
All the user has to do is work on the application. 

AWS Lambda is one serverless compute option. 
We can upload our call into a Lambda Function, configure some sort of trigger, and from there. 
When the trigger detected, the code is run automatically, in the managed environment: they automatically scale, highly available and all of the maintenance is done by AQWS. 
If there is 1 or 10000 triggers, Lambda will scale. 
These aren't for long running processes, < 15 minutes. 
More for quick, processing. 

If you weren't quite ready for serverless yet or you need access to the underlying environment, but still want efficiency and portability, you should look at AWS container services like Amazon Elastic Container Service, otherwise known as ECS. Or Amazon Elastic Kubernetes Service, otherwise known as EKS.

A container, here, is a Docker Container. 
Docker is a widely used platform, using OS level virtualization to deliver software in containers. 
A container is aw package for your code where you package up your application, the dependencies as well, and any configuration that it needs to run. 
These containers run on top of EC2 instances, running in isolation, similar to how the virtual machines work too. 
The host this time is the EC2 instance. 
They run in a cluster, all together. 
We also need to monitor these containers, the start, stop, restart, and the monitoring of thes containers, running on not just one of these EC2. 

The process of this is container orchestration and it turns out it's really hard to do on one's own. 

These tools are made to orchestrate these containers. 

ECS is run to help you run your containerized applications at scale without the hassle of managing your own container orchestration software. 

EKS does a similar thing. 

They run on top of EC2. 

You can also use Fargate, is a serverless compute platform for ECS and EKS. 

If we want to host traditional apps, want full access to the underlying OS, you are going to want to use EC2. 

If you want short running functions, service-orientated or event driven applications, and don't want to manage the underlying environment at all, serverless AWS Lambda is our guy. 

Docker containers, then we need to think about an orchestration tool. We have to choose between EKS or ECS. 
Do you want to run your containers on EC2 instances that you manage or in a serverless environment like AWS Fargate, that will be managed for you. 

### Serverless Computing
EC2, a service letting you run virtual servers in the cloud. 
If we want to use this, then we need to do the following: 
1. Provision instances (virtual servers). 
2. Upload your cod 
3. Continue to manage the instances while your application is running. 

Serverless just meaning that they will run our code, and we do not need to think about them at all. You can just focus on the app that we are writing etc. 

AWS Lambda is a good service for that. 

#### AWS Lambda
Allowing us to run our code without needing to provision or manage servers. 
Paying for only the computer time that we use. 
There are triggers for when we run this code: 
![[Pasted image 20250127151336.png]]
#### Containers
Standard way to package your application's code and dependencies into a single object. 
They can also be used for processes and workflows, there are essential requirements for security, reliability and scalability. 

![[Pasted image 20250127151658.png]]Environment staying consistent for all developers, and when they deploy, the environment will be consistent there as well. That's why you would want to use a containerized approach here as well. 
There is a reduced time in debugging apps due to computing environment differences. 

Consider scalability. Imagine there are 100's of hosts, each with 100's of containers. Imagine that we had to then monitor and sort out all those hosts. 

Container orchestration helps to deploy, manage, and scale your containerized apps. 
