# Azure_SDK_Java_Sample2_Create_StorageAccount

## 1. Main.java

```java
package com.example;

import com.azure.core.credential.TokenCredential;
import com.azure.core.management.AzureEnvironment;
import com.azure.core.management.profile.AzureProfile;
import com.azure.identity.DefaultAzureCredentialBuilder;
import com.azure.resourcemanager.AzureResourceManager;
import com.azure.core.management.Region;
import com.azure.resourcemanager.storage.models.StorageAccount;
import com.azure.resourcemanager.storage.models.StorageAccountSkuType;

public class Main {
    public static void main(String[] args) {

        //=============================================================
        // 1. Azure SDK for Java: Azure Authentication
        //=============================================================

        AzureProfile profile = new AzureProfile(AzureEnvironment.AZURE);
        TokenCredential credential = new DefaultAzureCredentialBuilder()
            .authorityHost(profile.getEnvironment().getActiveDirectoryEndpoint())
            .build();
        AzureResourceManager azureResourceManager = AzureResourceManager
            .authenticate(credential, profile)
            .withDefaultSubscription();
        
        //=============================================================
        // 2. Azure SDK for Java: Create a resource group
        //=============================================================

        final String rgName = "myFirstResourceGroup";
        System.out.println("Creating a resource group with name: " + rgName);

        azureResourceManager.resourceGroups().define(rgName)
             .withRegion(Region.US_WEST)
             .create();

        //=============================================================
        // 3. Create storage account
        //=============================================================

        String resourceName1 = "mystorageaccount19999"; 

        System.out.println("Creating a storage account with name: " + resourceName1);

        StorageAccount storageAccount = azureResourceManager.storageAccounts().define(resourceName1)
                .withRegion(Region.US_WEST)
                .withExistingResourceGroup(rgName)
                .create();

        System.out.println("Storage account created: " + storageAccount.id());

        //=============================================================
        // 4. Update - set the SKU name
        //=============================================================

        System.out.println("Updating the storage account with name: " + resourceName1);

        storageAccount.update()
                .withSku(StorageAccountSkuType.STANDARD_RAGRS)
                .apply();

        System.out.println("Updated the storage account with name: " + resourceName1);

        //=============================================================
        // 5. Create another storage account
        //=============================================================

        String resourceName2 = "mystorageaccount20000"; 

        System.out.println("Creating another storage account with name: " + resourceName2);

        StorageAccount storageAccount2 = azureResourceManager.storageAccounts().define(resourceName2)
                .withRegion(Region.US_WEST)
                .withExistingResourceGroup(rgName)
                .create();

        System.out.println("Storage account created: " + storageAccount2.id());

        //=============================================================
        // 6. List storage accounts.
        //=============================================================

        System.out.println("Listing all storage accounts for resource group: " + rgName);

        for (StorageAccount sAccount : azureResourceManager.storageAccounts().list()) {
            System.out.println("Storage account: " + sAccount.name());
        }

        //=============================================================
        // 7. Delete a storage accounts
        //=============================================================

        // System.out.println("Deleting storage account: " + resourceName2);

        // azureResourceManager.storageAccounts().deleteById(storageAccount2.id());

        // System.out.println("Deleted storage account: " + resourceName2);
    }
}
```

## 2. pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
        <!-- Azure SDK Dependencies -->
       <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-identity</artifactId>
            <version>1.11.1</version>
        </dependency>
        <dependency>
            <groupId>com.azure.resourcemanager</groupId>
            <artifactId>azure-resourcemanager</artifactId>
            <version>2.33.0</version>
        </dependency>
    </dependencies>

    <build>
            <plugins>
                <plugin>
                    <!-- Maven JAR Plugin Configuration -->
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-jar-plugin</artifactId>
                    <version>3.1.0</version>
                    <configuration>
                        <archive>
                            <manifest>
                                <mainClass>com.example.Main</mainClass>
                            </manifest>
                        </archive>
                    </configuration>
                </plugin>

                <plugin>
                    <!-- Exec Maven Plugin Configuration -->
                    <groupId>org.codehaus.mojo</groupId>
                    <artifactId>exec-maven-plugin</artifactId>
                    <version>3.1.0</version>
                    <configuration>
                        <mainClass>com.example.Main</mainClass>
                    </configuration>
                    <executions>
                        <execution>
                            <goals>
                                <goal>java</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>
            </plugins>
        </build>

</project>
```
```
