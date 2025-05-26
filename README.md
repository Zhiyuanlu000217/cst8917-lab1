# cst8917-lab1

Demo link: https://youtu.be/XssjScvWYmc

## 1. Connect Azure Functions to Azure Storage using Visual Studio Code

### Prequest

- [Azure Storage extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) for VS Code
- [Azure Storage Explorer](https://azure.microsoft.com/en-us/products/storage/storage-explorer#Download-4)
- Azure Subscription
- Finish [Quickstart: Create a C# function in Azure using Visual Studio Code](https://learn.microsoft.com/en-us/azure/azure-functions/create-first-function-vs-code-csharp)

### Downlaod function app settings

> Reference Tutorial: [Connect Azure Functions to Azure Storage using Visual Studio Code](https://learn.microsoft.com/en-us/azure/azure-functions/functions-add-output-binding-storage-queue-vs-code?pivots=programming-language-python&tabs=isolated-process#download-the-function-app-settings)


1. In vscode, press `F1` then choose `Azure Functions: Download Remote Settings...`
2. Choose the function app created in [previour tutorial](https://learn.microsoft.com/en-us/azure/azure-functions/create-first-function-vs-code-csharp)
3. You will get a `local.settings.json`, which include the credentials of Azure Storage connection string.
4. Move the `local.settings.json` file into `/part1` directory.
5. Move to `/part1` directory, activate python venv, and install dependencies.

> [!Note]  
> `Dependencies` refers to the library that python project required. Please make sure all extensions and runtime is configured properly.

### Test the function locally

6. Press `F5` to start the function.
7. Go to Side Panel > Azure > Workspace > Local Project > Functions. Right click on `HttpExample` and choose 'Execute Function Now'. Then Press `Enter` when it requests the message body.
8. Once the function triggered, you will see the message indicating function executed, with response saying `Hello Azure ...` : ![Trigger function locally](images/part1-trigger-function-locally.png)

9. Open Azure Storage Explorer, and sign in with Azure account by following the prompt.
10. Goto your subscription > Storage accounts > your storage account (which created in previous tutorial) > Queues > outqueue, you should see following: ![Trigger function locally](images/part1-q-first-run.png)

11. Try to run the function again, and refresh the tab. Now there should be two messages: ![Trigger function locally](images/part1-trigger-second-locally.png)

### Deploy the app and trigger it from cloud
12. Deploy the app

> [!Note]  
> In [official tutorial](https://learn.microsoft.com/en-us/azure/azure-functions/functions-add-output-binding-storage-queue-vs-code?pivots=programming-language-python&tabs=isolated-process#redeploy-and-verify-the-updated-app), it asked to redeploy on the original `C#` project. Which it will fail because the runtime is different since we are using `python`, and the runtime is not able to get changed.

Press `F1`, choose `Azure functions: create funtion app...`, then follow the prompts to create a new function app. Then, Press `F1` again, choose `Azure functions: deploy to function app`, then deploy to the function that you just created. In this way, we can bypass the runtime issue. Note that you must change the `AzureWebJobsStorage` located in your newly created project to the one we get in Step3.

13. Trigger the function by pressing `F1` > `Azure functions: Execute functions now` > Then follow the prompt to locate the newly deployed function.

14. Check Storage Explorer, a new message should be added.



## 2. Connect Azure Functions to Azure SQL Database using Visual Studio Code


### Prequest

- Finish Part 1
    - Code
    - Deployed function app

### Create Azure SQL Database

1. In the previous part resource group, go to create > azuer sql, locate "SQL Databases", choose Single database, then create.
2. Database name enter `mySampleDatabase`, serer name can be any unique name, authentication method selecte `SQL Server authentication`, user name should be set to `azureuser`. 
3. In `Networking` Tab, `Allow Azure services and resources to access this server` should be toggled as yes. Note this option is not displayed by default, you need to change the database access to `public` to see this option.
4. Click Create.

### Configure Azure SQL Database

5. first go to the database you created, in side panel > Settings > Connection Strings, locate the `ADO .NET` connection string(Which is the one at bottom), copy the string, and leave it in a temporary file. Make sure you modify the password to your own.
For example, the connection string is 
```
Server=tcp:cst8917lab1.database.windows.net,1433;Initial Catalog=mySampleDatabase;Persist Security Info=False;User ID=azureuser;Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```
and you have a password `abcd123456DEF`, 
make sure you replace `Password={your_password}` into `Password=abcd123456DEF`.

6. Then goto side panel > Query Editor(Preview), login with your password. If you did not configure the allowed ip address in last step, there will be a prompt allow you to configure it by one-click.

7. Once logged in, Create a table
```sql
CREATE TABLE dbo.ToDo (
    [Id] UNIQUEIDENTIFIER PRIMARY KEY,
    [order] INT NULL,
    [title] NVARCHAR(200) NOT NULL,
    [url] NVARCHAR(200) NOT NULL,
    [completed] BIT NOT NULL
);
```
by paste the query above and hit `Run` button.

### Update Function app

8. Press `F1`, find `Azure Functions: Add New Setting`, then choose the function app we created in Part 1, enter the name `SqlConnectionString`, and the value we get in step 5.

9. Press `F1`, find `Azure Functions: Download Remote Settings`, allow overwrite the existing local settings. You will find a new field `SqlConnectionString` added in `local.settings.json`.

### Run the update code

10. You can run the code loacted in `/part2` by pressing `F5` like we did in part 1. When we run it locally, just remain the body value by default.
11. Once the function is executed successfully, you can go to Azure Sql Database > Query editor(Preview), right click on the `dbo.ToDo` table and select Top 1000 Rows. For now, you will see only one row.

### Deploy the code

12. Press `F1`, find `Azure Functions: Deploy to function app...`, choose the function app we have. (Note: if you do not want to overwrite the previous trigger, you can paste the old one into `function_app.py`)

13. After deploy, you can press `F1`, and find `Azure Functions: Execute Function Now...`.

14. Now, if you go back to Query editor, and select Top 1000 Rows again, you will see two rows.



> [!Important]  
> Please do not forget to clean up all the resources created during the tutorial.