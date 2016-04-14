# Channels as Devices

## Actors

* Channel Service
* Credentials Device
* User Device
* User

## Scenario 1

Peter wants to use the Octoblu Twitter Account in a flow.

So Peter visits Octoblu and to add a new Twitter device. He gets bounced out to the **twitter-service** to start the process.

The first thing that the **twitter-service** will need to do is verify that Peter has control over the Octoblu twitter account. It does this via Oauth 2.0, resulting in a twitter `account_id` and an `access_token`.

The **twitter-service** creates a new **Credentials Device** to store the `account_id` and `access_token` in encrypted form. The **twitter-service** adds a `message.received` webhook to the **Credentials Device** with `generateAndForwardMeshbluCredentials`, so that it will post up to the **twitter-service** with a `uuid` and `token` of the **Credentials Device** whenever it receives a message. Peter does not get access to this device.

It then creates a **User Device** for Peter. The **User Device** has Peter its `configure.update` whitelist, the **Credentials Device** in its `message.as` whitelist. The **twitter-service** creates a `message.received` subscription to the **User Device** on behalf of the **Credentials Device**.

Now, whenever the **User Device** is messaged, the **Credentials Device** will receive it, and post up to the **twitter-service**. The **twitter-service** will use the **Credentials Device** credentials to retrieve the encrypted credentials, decrypt them using its private key, and then perform the action requested in the message.

## Scenario 2

Aaron wants to use the Octoblu Twitter Account in a flow. Peter is already using that twitter account. Aaron knows the account's password.

## Scenario 3

Erik wants to use the Octoblu Twitter Account in a flow. Aaron and Peter are already using that twitter account. Andrew is just an intern, and cannot be trusted, so they do not want to disclose the password to him.
