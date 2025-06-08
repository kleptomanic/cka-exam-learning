# Day 1: Kubernetes Introduction

This module provides a beginner-friendly introduction to **Kubernetes**, a leading platform for managing containerized applications. It covers the **purpose of Kubernetes**, **container orchestration**, and its **use cases**, along with details about the **Cloud Native Computing Foundation (CNCF)**, the **Certified Kubernetes Administrator (CKA) exam**, **career possibilities**, and **other certifications**. The module includes a demo using a CKA exam simulator, a hands-on lab to explore Kubernetes documentation, recommended resources, and a cheatsheet for exam strategies. Additionally, it outlines the importance of **CNCF**, **CKA certification**, and **Kubernetes** in the tech ecosystem.

---

## Topics

### **Kubernetes Purpose**

![Kubernetes Logo](https://raw.githubusercontent.com/cncf/artwork/refs/heads/main/projects/kubernetes/horizontal/all-blue-color/kubernetes-horizontal-all-blue-color.png)

**Kubernetes** (also known as **K8s**) is an open-source platform that simplifies the management of containerized applications. **Containers** are lightweight, portable units that package an application with its dependencies, ensuring it runs consistently across different environments. **Kubernetes** automates the deployment, scaling, and operation of these containers, making it easier to manage complex applications.

- **Why Use Kubernetes?**
  - **Automates Tasks**: Handles starting, stopping, and updating applications automatically.
  - **Portable**: Runs on local machines, public clouds (e.g., AWS, Google Cloud), or private servers.
  - **Reliable**: Restarts failed apps and ensures high availability.
  - **Scalable**: Adjusts the number of app instances based on demand.

- **Example**: Think of **Kubernetes** as a manager for a busy restaurant, ensuring all staff (containers) work together to serve customers (users) efficiently.

### **Container Orchestration**
**Container orchestration** involves coordinating multiple **containers** to function as a unified system. **Kubernetes** acts like an orchestra conductor, ensuring all parts of an application (e.g., web servers, databases) run smoothly, communicate effectively, and recover from failures.

- **Key Functions**:
  - **Scheduling**: Places **containers** on appropriate servers based on resource needs.
  - **Load Balancing**: Distributes user traffic across **containers** to prevent overload.
  - **Self-Healing**: Restarts or replaces failed **containers** automatically.
  - **Updates**: Rolls out new app versions without downtime.
  - **Scaling**: Adds or removes **containers** based on workload demands.

- **Key Components**:
  - **Pod**: The smallest unit in **Kubernetes**, containing one or more **containers** that share resources.
  - **Node**: A server (physical or virtual) that runs **pods**. There are two types:
    - **Control Plane Node**: Manages the **cluster**, handling tasks like scheduling and monitoring. Components include the **API server**, **scheduler**, and **controller manager**.
    - **Worker Node**: Runs **pods** with application workloads, managed by the **control plane**.
  - **Cluster**: A group of **nodes** (control plane and worker) managed by **Kubernetes**.

![Kubernetes Architecture](https://kubernetes.io/images/docs/kubernetes-cluster-architecture.svg)

### **Use Cases**
**Kubernetes** is widely used across industries to manage modern applications. Its flexibility makes it ideal for various scenarios:

- **Microservices**: Runs applications split into small, independent components (e.g., one **container** for a website’s frontend, another for its backend).
- **Continuous Deployment**: Supports automated pipelines for releasing new app versions.
- **Multi-Cloud**: Enables apps to run across different clouds or on-premises servers.
- **Big Data & AI**: Manages complex workloads like data processing or machine learning.
- **Critical Apps**: Ensures 24/7 availability for services like e-commerce or banking.

- **Examples**:
  - Netflix uses **Kubernetes** to stream videos to millions of users.
  - Gaming companies rely on it to handle sudden player spikes.
  - Startups use it to build scalable, cost-effective applications.

### **CNCF Details**

![CNCF Logo](https://raw.githubusercontent.com/cncf/artwork/3b2b80eb757282ccf62227c35d66f21e0688a6e6/other/cncf/horizontal/color/cncf-color.svg)

The **Cloud Native Computing Foundation (CNCF)** is a non-profit organization under the **Linux Foundation**, founded in 2015, dedicated to advancing **cloud-native technologies** like **Kubernetes**, **Prometheus**, and others. It fosters collaboration among companies and developers to build scalable, resilient systems.

- **Key Details**:
  - **Founded**: 2015, as part of the **Linux Foundation**.
  - **Mission**: Promotes **cloud-native** practices, focusing on containerized, scalable applications.
  - **Certifications**: Manages **CKA**, **CKAD**, **CKS**, and **KCNA** to validate **Kubernetes** expertise.
  - **Members**: Over 200 organizations, including tech giants like **Google**, **AWS**, and **Microsoft**.
  - **Projects**: Hosts open-source tools like **Kubernetes**, **Prometheus**, and **Envoy**.

- **Importance of CNCF**:
  - Drives standardization and adoption of **cloud-native** technologies.
  - Provides certifications to validate skills, boosting career prospects.
  - Builds a community for collaboration, ensuring tools like **Kubernetes** evolve with industry needs.

### **CKA Exam Details**

![CKA Logo](https://raw.githubusercontent.com/cncf/artwork/refs/heads/main/other/cka/color/kubernetes-cka-color.png)

The **Certified Kubernetes Administrator (CKA)** exam, offered by the **CNCF**, tests your ability to administer **Kubernetes** clusters. It’s a hands-on, performance-based exam for professionals aiming to prove their skills.

- **Exam Format**:
  - **Duration**: 2 hours.
  - **Tasks**: 15–20 practical tasks (e.g., create a **pod**, configure a **cluster**).
  - **Passing Score**: 66% or higher.
  - **Environment**: Remote desktop with a terminal, browser, and access to [kubernetes.io](https://kubernetes.io/).
  - **Cost**: $395 (includes one free retake).

- **Tips**:
  - Practice with real **Kubernetes** clusters (e.g., Minikube, Kind).
  - Master **kubectl** commands and navigating [kubernetes.io](https://kubernetes.io/).
  - Focus on time management due to the exam’s tight schedule.

### **Career Possibilities**
**Kubernetes** skills are in high demand as companies adopt **cloud-native** technologies, opening doors to rewarding tech careers.

- **Roles**:
  - **DevOps Engineer**: Automates app deployment and infrastructure.
  - **Cloud Engineer**: Designs and manages cloud systems (e.g., **AWS**, **Google Cloud**).
  - **Site Reliability Engineer (SRE)**: Ensures apps are reliable and scalable.

- **Demand**:
  - High demand in tech giants (**AWS**, **Google**), startups, and industries like finance and healthcare.
  - Growing adoption in **cloud-native ecosystems** (per **CNCF** surveys, 2025).

- **Salary**:
  - Average U.S. salary: $100,000–$150,000 per year (Glassdoor, 2025).
  - Varies by experience, location, and company size.

- **Growth**:
  - **Kubernetes** expertise leads to senior roles like Cloud Architect or DevOps Lead.
  - Complements skills in **Docker**, **CI/CD**, and cloud platforms.

### **Other Certifications**
The **CNCF** offers additional certifications to enhance your **Kubernetes** expertise:

- **CKAD (Certified Kubernetes Application Developer)**:
  - Focus: Developing and deploying applications on **Kubernetes**.
  - Ideal for developers building **cloud-native** apps.
- **CKS (Certified Kubernetes Security Specialist)**:
  - Focus: Securing **Kubernetes** clusters and workloads.
  - Suited for cybersecurity professionals.
- **KCNA (Kubernetes and Cloud Native Associate)**:
  - Focus: Entry-level knowledge of **Kubernetes** and **cloud-native** basics.
  - Great for beginners or non-technical roles.

---

## Importance of CNCF, CKA Certification, and Kubernetes

- **CNCF**:
  - **Standardization**: Sets standards for **cloud-native** tools, ensuring interoperability and reliability.
  - **Community**: Fosters collaboration among 200+ member organizations, driving innovation in tools like **Kubernetes**.
  - **Certifications**: Offers globally recognized credentials (**CKA**, **CKAD**, **CKS**, **KCNA**) that validate skills and boost employability.
  - **Impact**: Accelerates adoption of **cloud-native** technologies, shaping the future of software development.

- **CKA Certification**:
  - **Validation**: Proves your ability to manage **Kubernetes** clusters, a critical skill in modern IT.
  - **Career Boost**: Increases job prospects in high-demand roles like **DevOps Engineer** and **SRE**.
  - **Practical Focus**: Tests real-world skills through hands-on tasks, ensuring readiness for industry challenges.
  - **Global Recognition**: Backed by **CNCF**, respected by employers worldwide.

- **Kubernetes**:
  - **Industry Standard**: Powers applications at companies like Netflix, Spotify, and **Google**, making it a must-know technology.
  - **Scalability**: Enables businesses to handle growth and traffic spikes efficiently.
  - **Flexibility**: Supports diverse environments (cloud, on-premises, hybrid), reducing vendor lock-in.
  - **Future-Proof**: Drives **cloud-native** development, aligning with modern software trends.

---

## Demo: Show Killer.sh Simulator and Bookmark kubernetes.io

This demo introduces the **Killer.sh** CKA simulator and highlights the importance of [kubernetes.io](https://kubernetes.io/) for exam preparation.

1. **Killer.sh Simulator**:
   - Visit [Killer.sh](https://killer.sh/) to access a practice environment for the **CKA** exam.
   - Try a sample task (e.g., creating a **pod**) to get familiar with the exam’s hands-on format.
   - Note the timer and interface, which mimic the real exam.

2. **Bookmark kubernetes.io**:
   - The official **Kubernetes** documentation ([kubernetes.io](https://kubernetes.io/)) is allowed during the **CKA** exam.
   - Bookmark sections like **Concepts**, **Tasks**, and **Reference** for quick access.
   - Practice finding **kubectl** commands or **pod** documentation.

![Killer.sh Interface](https://killer.sh/assets/images/killersh-screenshot.png)

---

## Lab: Explore kubernetes.io

This lab helps you navigate the **Kubernetes** documentation, a key resource for the **CKA** exam and learning.

### Steps
1. **Visit kubernetes.io**:
   - Open [kubernetes.io](https://kubernetes.io/) and explore the **Concepts** section.
   - Understand basic terms like **pod**, **node**, and **cluster**.

2. **Search for “Pod”**:
   - Use the search bar to find “pod.”
   - Read the [Pods documentation](https://kubernetes.io/docs/concepts/workloads/pods/).
   - Note what a **pod** is and its role in **Kubernetes**.

3. **Explore Other Sections**:
   - Check **Tasks** for practical guides (e.g., “Run a Stateless Application”).
   - Review **Reference** for **kubectl** commands and API details.

### Goal
- Build confidence in finding information quickly, as you’ll rely on [kubernetes.io](https://kubernetes.io/) during the **CKA** exam.

---

## Resources
- **[CNCF.io](https://www.cncf.io/)**: Learn about **CNCF** projects and **cloud-native** technologies.
- **CNCF CKA FAQ**: [CNCF CKA Page](https://www.cncf.io/certifications/cka/) for exam details and registration.
- **KillerCoda**: [KillerCoda](https://killercoda.com/) for interactive **Kubernetes** labs.
- **Glassdoor**: [Glassdoor Salaries](https://www.glassdoor.com/Salaries/) for **Kubernetes**-related job salary data (2025).
- **Linux Foundation**: [linuxfoundation.org](https://training.linuxfoundation.org/) for details on **CKA**, **CKAD**, **CKS**, and **KCNA**.

---

## Cheatsheet: Exam Strategies

| Strategy                | Description                                                                 |
|-------------------------|-----------------------------------------------------------------------------|
| **Time Management**     | Review all tasks first; complete quick ones (e.g., `kubectl get`) before complex ones. |
| **Context Switching**   | Use `kubectl config use-context` to switch between **clusters**; check with `kubectl config current-context`. |
| **Use kubernetes.io**   | Search [kubernetes.io](https://kubernetes.io/) for **kubectl** commands and examples. |
| **Verify Work**         | Confirm tasks (e.g., `kubectl get pods` to check **pod** status).            |
| **Stay Calm**           | Skip difficult tasks and return later to avoid wasting time.                 |

---

This module lays the foundation for understanding **Kubernetes**, the role of **CNCF**, and the value of **CKA certification**. Practice with the lab and explore the resources to prepare for the exam and a career in **cloud-native** technologies!