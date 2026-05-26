# Azure-Compute-and-Identity-Management
This project involves deploying a virtual machine, securing it using RBAC, applying Azure Policies for resource governance, encrypting sensitive data, and monitoring costs. It teaches foundational concepts for managing Azure identities, governance, and compute resources.


## **Scenario**

A company wants to deploy a virtual machine for their web application, secure it with role-based access control, enforce a policy for naming conventions, encrypt sensitive data, and monitor the cost of the deployed resources.

---

##📋 **Steps**

### **1. Create a Virtual Machine**

- Go to **Azure Portal** > **Virtual Machines** > **Create**.
- Select:
  - Subscription
  - Resource group
  - Region
  - VM size
  - Operating system (Windows/Linux)
- Configure:
  - Admin username/password or SSH key
- Add a **data disk** during creation:
  - Create a new disk > None.
  - If you select Storage Blob, this allows reuse of disks from outside Azure or custom uploaded configurations.
- Configure **Networking**:
  - Assign a static private IP via the **Advanced Networking** section.
  - Use a **Network Security Group (NSG)** to allow or block traffic.

#### **NSG Options**

- **None**
  - Use if managing NIC setup manually or attaching an existing NIC.

- **Basic**
  - Use for quick/simple deployments such as testing or small-scale apps.

- **Advanced**
  - Use for production workloads or custom network configurations requiring granular control.

- Review and create the VM.
- Download the private key.

---

### **2. Configure Role-Based Access Control (RBAC)**

#### **Create a Key Vault and Store the Private Key**

- Go to:
  - **Key Vault** > **Create**
  - Use the same resource group and region.
- Go to:
  - **Entra** > **Create Group**
  - Add yourself as owner and member.
- Assign permissions:
  - **Key Vault** > **IAM** > **Add Role Assignment**
  - Select:
    - **Key Vault Admin**
    - Users
    - Your new group
  - Review and assign.

> Note:
> - Role propagation may take time.
> - Sign out and back in to refresh your Azure token.
> - Alternatively, assign permissions directly to yourself.

- Upload the private key:
  - **Key Vault** > **Objects** > **Keys** > **Generate/Import** > **Import**
  - Upload the key file.

#### **Assign VM Permissions**

- Navigate to:
  - **VM** > **Access Control (IAM)** > **Add Role Assignment**
- Assign:
  - **Virtual Machine Contributor**
  - or a custom role
- Verify permissions:
  - **Entra** > **Manage** > **Groups** > **All Groups** > Your Group

---

### **3. Log into the VM**

- Go to:
  - **VM** > Your VM > **Connect** > **SSH using Azure CLI** > **Configure**
- In the Azure CLI window, run:

```bash
az network public-ip list
```

- Test the public IP in a browser:
  - Verify that only SSH access is allowed.
  - HTTPS access should be blocked by the NSG.

---

### **4. Apply Azure Policy**

- Navigate to:
  - **Azure Policy** > **Authoring** > **Definitions**
  - **Initiative Definition**
- Configure:
  - Name
  - Category
  - Next

#### **Add Policy Definitions**

- Add policy definitions:
  - Search `tag`
  - Select:
    - “Inherit a tag from resource group…”
    - “Inherit a tag from subscription_name”
- Search `allow`
  - Select:
    - “Allowed resource types”
- Review + Create > Next

#### **Create Policy Group**

- Create Group:
  - **Tags** > Save
  - Previous
  - Select tag policies
  - Add to group
  - Select Tags
  - Save

#### **Create Initiative Parameter**

- Select:
  - **Initiative Parameters**
  - Create initiative parameter
- Configure:
  - Name
  - DisplayName
  - Allowed Values
- Save

#### **Configure Reference IDs**

For:
- “Inherit a tag from resource group…”
- “Inherit a tag from subscription_name”

Set:
- **Value Type** = Use Initiative Parameter
- **Value(s)** = Name

For:
- Allowed resource types:
  - Set value to:
    - `"virtualMachines"`

- Review and create > Create

#### **Assign and Test Policy**

- Assign the initiative to the subscription:
  - Select subscription in Basics
  - Configure parameters
  - Review + Create > Create

- View compliance:
  - **VM** > **Operations** > **Policies**

---

### **5. Monitor and Manage Costs**

- Navigate to:
  - **Cost Management + Billing**
  - **Billing Scope**
  - **Cost Management**
  - **Budgets**
  - **Add**

#### **Configure Budget**

- Set:
  - Monthly spending limit
- Configure notifications:
  - Email or Action Groups
  - Thresholds:
    - 50%
    - 80%
    - 100%

#### **Review Costs**

- Review:
  - **Cost Analysis**
    - Identify trends
    - Optimize spending

- Review:
  - **Advisor Recommendations**
    - View cost optimization recommendations
