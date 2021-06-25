# Azure Kubernetes Service : Cost Optimization Recommendations

Cost optimization is a very important pillar while we are designing a cloud solution that can be Azure Kubernetes Services or any other service. We have to balance between cost and efficiency. In this article, I'll handle the Azure Kubernetes Service (AKS) cost optimization based on Well Architected Framework (WAF) suggestions. First, I'll give a brief about WAF principles and then give exact recommendation for AKS.

WAF cost optimization pillar has different stage for Azure journey, these're general suggestion for running a workload on Azure. 

	1- Design:  Determine requirements according the business value you need.
		a. Collect requirements for developing new workload, 
			i. business requirements, performance and reliability requirement, storage requirements etc.
		b. Estimate costs
			i. Azure Pricing Calculator
		c. Organize resources for cost awareness
			i. Use Azure Policy to create limitations for the VM size or etc.
			
	2- Provision: Build your workload. 
		a. Select right SKU / service tier / sizes
		b. Use spot instances for low priority workloads.
		c. Use reserved instances
		d. Use managed service for lower maintenance cost.
		
	
	3- Monitor: Monitor and review spending with stakeholders. 
		a. Track you cloud spend
			i. Azure Cost Management
			ii. Azure Advisor
	
	4- Optimize: Make your workload run more cost efficiently and deliver your business value with right size.
		a. Choose a smaller size for the VM instance
		b. Reduce the number of Virtual Machine running hours if you don't need (use auto shutdown or use Azure Automation )
		c. Use discounts such as Hybrid Benefit.
		d. Implement shutdown schedules for Virtual Machines.

Let's discuss around AKS cost optimization based on above stages. 


## 1- Select Right Size

AKS cluster management is actually free, users pay for the nodes which run containers that means users only pay for Virtual Machines, storage and data transfer costs. So selecting correct VM size is very important of AKS cluster cost. 
		a. Production clusters : If you don't have special requirement like GPU, general recommendation for production environments are VM series **DSv3 and ESV3**. **D2s_v3 (2 vCPU, 8 GB Memory)  or D4s_v3 (4 vCPU, 16 GB Memory)**  may be a good starting point. If you need a higher cpu and memory , you can select ESV3 series. 
		b. Dev/Test clusters : **Standard_B2ms, Standard_B4ms or Standard_B8ms** size are good enough to reduce the cost of cluster for dev/test environments. 
		
## 2- Use Reserved Instances 

Reserved instances help you save money by committing to one-year or three-years usage.  if you've purchased Azure reservations for the VM size that you're using in AKS, those discounts are automatically applied.
	
## 3- Scale node pools to 0   

Node pools contain the virtual machines that run our pods / applications. AKS has two different node pools which are System and User. System node pools has critical system pods such CoreDNS and metrics-server and that requires always running. User node pools run our application pods. You can have some application and runs only a couple of hours in a day enough or that may be a batch application and you don't need always running VMs. In this case, you should scale node pools to zero. This guide helps you to learn how to scale to 0. https://docs.microsoft.com/en-us/azure/aks/scale-cluster#scale-user-node-pools-to-0 
	
## 4- Add spot node pool 

Azure Spot Virtual Machine instances are great for workloads that can handle interruptions like batch processing jobs, dev/test environments, large compute workloads, and more. And we can create a spot node pool which backed by a spot virtual machine scale sets. When deploying a spot node pool, Azure will allocate the spot nodes if there's capacity available. But there's no SLA for the spot nodes. A spot scale set that backs the spot node pool is deployed in a single fault domain and offers no high availability guarantees. At any time when Azure needs the capacity back, the Azure infrastructure will evict spot nodes. For detail click here
	 
## 5- Use Azure Policy for AKS resource quotas 

Azure Policy helps use to enforce usage/limits of resources and Azure Policy extends Open Policy Agent (OPA) to integrate AKS with built-in policies. For example, you must carefully attacks such as denial of service attack which can increase resource usage. You can use "Ensure container CPU and memory resource limits do not exceed the specified limits in Kubernetes cluster" policy to prevent resource exhaustion attack.
	
## 6- Cost governance

Cost governance is the continuous process of implementing policies to control costs.  In the Kubernetes context, there are several ways organizations can control and optimize costs. These include native Kubernetes tooling to manage and govern resource usage and consumption and proactively monitor and optimize the underlying infrastructure. You can use **Kubecost** to govern AKS cluster costs which can scope cost allocation to a deployment, service, label, pod, or namespace, which provides flexibility in charging back or showing cluster users.  See there for more information. https://docs.kubecost.com/install


References:

- http://docs.microsoft.com/en-US/azure 
- https://channel9.msdn.com/
- http://docs.kubernetes.io/
