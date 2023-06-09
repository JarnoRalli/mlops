# 1 Microsoft Azure MLOps

Microsoft Azure MLOps related examples.

# 2 Folder Structure

* [Environment](environment/README.md)
  * Infrastructure-as-Code (IaC) for generating resources for MLOps
* [Docker](docker/README.md)
  * Docker files for running self-hosted CI runners in Azure

# 3 CLI Tools

## 3.1 AzCopy

The easiest way of copying data to an Azure container appears to be using a CLI tool called `azcopy`. For installing 
version 10, take a look [here](https://learn.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10). Before using
AzCopy on a resource, such as a storage blob, you first need to assign roles, such as:

* `Storage Blob Data Contributor`
* `Storage Queue Data Contributor`

to the entity (user, group, service principal or managed identity) using the storage. Take a look at these instructions [https://learn.microsoft.com/en-us/azure/role-based-access-control/role-assignments-portal](https://learn.microsoft.com/en-us/azure/role-based-access-control/role-assignments-portal).

### 3.1.1 Improved Login Support for AzCopy

> Starting AzCopy 10.7.0, (make, list, copy, and sync) commands can authenticate to Azure storage by picking up Azure Active Directory credentials 
> from environment variables, without the need to first invoke the login command. AzCopy does not store the AAD token on disk, and discards it 
> after the AzCopy operation ends. Also, please note that these credentials cannot be used to login to Azure Files, which still requires a SAS key.
>
> By default, AzCopy attempts to login to default tenant, and AAD endpoint for Azure public cloud (https://login.microsoftonline.com). 
> To override this, set the environment variables AZCOPY_TENANT_ID, and AZCOPY_ACTIVE_DIRECTORY_ENDPOINT appropriately.

[Source](https://github.com/Azure/azure-storage-azcopy/wiki/Improved-login-support-for-AzCopy-commands-(with-in-memory-secret-store))

### 3.1.2 Authorize a User Identity

The `azcopy login` command retrieves an OAuth token and places it into a secret store on the system.

```bash
azcopy login --tenant-id=<TENANT-ID>
azcopy login status
```

You can get the `tenant-id` from `Azure Active Directory` -> `Properties` -> `Tenant ID`. If `azcopy login status` reports that you're not logged in,
then you can try the following:

```bash
sudo apt-get install keyutils
keyctl new_session
azcopy login --tenant-id=<TENANT-ID>
keyctl show
azcopy login status
```

### 3.1.3 Authorize a User Identity

If you operating system doesn't have a secret store such as a Linux `keyring`, you can set in-memory
environment variables.

* `export AZCOPY_AUTO_LOGIN_TYPE=DEVICE`
* `azcopy list <STORAGE-URI>`

### 3.1.4 Useful Commands

* `azcopy copy '<SOURCE>' '<STORAGE-URI>' --recursive`
  *  Copies a directory `<SOURCE>` to `<STORAGE-URI>`

## 3.2 Common Storage Commands

Before executing any of the storage commands, you can set the following environment variables for access control.
These are available from portal.azure.com, under `Storage accounts` -> `Access Keys`.

* `AZURE_STORAGE_CONNECTION`
  * When setting it, put it between `""`
* `AZURE_STORAGE_KEY`
  * You get this information from Storage accounts -> Access Keys
* `AZURE_STORAGE_ACCOUNT`
  * Name of the storage account

Following is a list of some useful commands related to storage:

* `az storage container list | grep name`
  * Lists storage containers
* `az storage blob upload --container-name <NAME> --file <FILE-NAME>`
  * Uploads a file called `<FILE-NAME>' to a container called `<NAME>`
