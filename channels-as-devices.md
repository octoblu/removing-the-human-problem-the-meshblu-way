# Channels as Devices

## Actors

* Channel Service
* Credentials Device
* User Device
* User

## Scenario 1

Peter wants to use the Octoblu Twitter Account in a flow.

So Peter visits Octoblu and to add a new Twitter device. He gets bounced out to the **twitter-service** to start the process.

The first thing that the **twitter-service** will need to do is verify that Peter has control over the Octoblu Twitter account. It does this via Oauth 2.0, resulting in a Twitter `account_id` and an `access_token`.

The **twitter-service** creates a new **Credentials Device** to store the `account_id` and `access_token` in encrypted form. The **twitter-service** adds a `message.received` webhook to the **Credentials Device** with `generateAndForwardMeshbluCredentials`, so that it will post up to the **twitter-service** with a `uuid` and `token` of the **Credentials Device** whenever it receives a message. Peter does not get access to this device.

It then creates a **User Device** for Peter. The **User Device** has Peter its `configure.update` whitelist, the **Credentials Device** in its `message.as` whitelist. The **twitter-service** creates a `message.received` subscription to the **User Device** on behalf of the **Credentials Device**.

Now, whenever the **User Device** is messaged, the **Credentials Device** will receive it, and post up to the **twitter-service**. The **twitter-service** will use the **Credentials Device** credentials to retrieve the encrypted credentials, decrypt them using its private key, and then perform the action requested in the message.

## Scenario 2

Aaron wants to use the Octoblu Twitter Account in a flow. Peter is already using that Twitter account. Aaron knows the account's password.

So Aaron also visits Octoblu and to add a new Twitter device. Like Peter, he gets bounced out to the **twitter-service** to start the process.

After the **twitter-service** has verified that Aaron has control over the Octoblu Twitter account, it now has the same `account_id`, but a different `access_token`. In fact, in the case of Twitter and many other APIs, the `access_token` that the **twitter-service** retrieved for Peter and stored on the **Credentials Device**, is no longer valid.

The **twitter-service** has to use the `account_id` to find the **Credentials Device** it created for Peter and update the encrypted `access_token` with the new one. Aaron also does not get access to the **Credentials Device**. Nobody but the **twitter-service** does, ever. Stop asking.

Now, the **twitter-service** creates Aaron his own shiny, new **User Device** (**TwitAaron**), setup the same as Peter's **User Device** (**TwitPeter**), except that with Aaron's UUID in the `configure.update` instead of Peter's.

The **twitter-service** will then inform Aaron of that the **TwitPeter** device exists, and will give Aaron the option of removing it's access, resulting in the **twitter-service** removing the **Credentials Device** `message.received` subscription to **TwitPeter**. Aaron chooses not to be a jerk (for once) and leaves **TwitPeter** alone.

Now Aaron can post messages to **TwitAaron**, who will proxy it up to the **Credentials Device**, just like **TwitPeter**.

## Scenario 3

Andrew wants to use the Octoblu Twitter Account in a flow. Aaron and Peter are already using that Twitter account. Andrew is just an intern, and cannot be trusted, so they do not want to disclose the password to him.

Andrew begs Peter to give him access to the Octoblu Twitter Account. After months of ignoring Andrew's groveling, Peter finally relents. He goes through the whole process described in **Scenario 2** to create a second **User Device** for himself, which we will call **TwitAndrew**.

He then adds Andrew to the **TwitAndrew** `configure.update` whitelist, so that when Andrew adds the device to a flow, Octoblu can add that flow to **TwitAndrew**'s `message.from` whitelist.

Andrew promptly abuses his new Tweeting powers by posting a bunch of cat pictures. Peter can now revoke Andrew's access by Oauth the **twitter-service** with Twitter again, and then removing **TwitAndrew** from the list of devices that the **Credentials Device** is subscribed to.
