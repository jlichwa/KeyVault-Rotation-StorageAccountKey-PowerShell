# KeyVault-Rotation-StorageAccountKey-PowerShell

## Key Vault Storage Account Key Rotation Functions

Functions regenerate individual key (alternating key1 and key2) in Storage Account and add regenerated key to Key Vault as new version of the same secret.

Functions require following information stored in secret as tags:
- $secret.Tags["ValidityPeriodDays"] - number of days, it defines expiration date for new secret
- $secret.Tags["CredentialId"] - key id (key1 or key2)
- $secret.Tags["ProviderAddress"] - Storage Account Resource Id

You can create new secret with above tags and Storage access key as value or add those tags to existing secret. For automated rotation expiry date would also be required - it triggers event 30 days before expiry

There are two available functions performing same rotation:
- AKVStorageRotation - event triggered function, performs storage account key rotation triggered by Key Vault events. In this setup Near Expiry event is used which is published 30 days before expiration
- AKVStorageRotationHttp - on-demand function with KeyVaultName and Secret name as parameters

## Rotation Setup - ARM Templates

There are 3 ARM templates available
- [Initial Setup](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell/tree/master/arm-templates#inital-setup)- Creates Key Vault and Storage Account if needed. Existing Key Vault and Storage Account can be used instead
- [Function Rotation - Complete Setup](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell/tree/master/arm-templates#storage-account-key-rotation-functions) - It creates and deploys function app and function code, creates necessary permissions, and Key 
Vault event subscription for Near Expiry Event for individual secret (secret name can be provided as parameter)
- [Adding additional secrets to existing function](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell/tree/master/arm-templates#add-event-subscription-to-existing-functions) - single function can be used for multiple storage accounts. This template adding new event subscription for secret and necessary permissions to access storage account