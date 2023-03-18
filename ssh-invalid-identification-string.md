# Problem

After updating the [ssh2-sftp-client](https://www.npmjs.com/package/ssh2-sftp-client) library, one user can no longer connect to an SFTP server. 

The error message is `Invalid identification string`.

# Investigation

When connecting from command line using the OpenSSH client, the error message is:

`Bad remote protocol version identification: 'SSH-2.0-'`

So it's indeed the server's fault. There should be a software name and version after `SSH-2.0-`, for example, `SSH-2.0-OpenSSH_9.0`.

Nevertheless, the user can't upgrade or modify the server as it's provided by an external partner.

# Solution

Locate the code responsible for verifying the identification header and make it allow an empty software part.

Turns out that the verification code is in `[ssh2](https://github.com/mscdex/ssh2)` library, a subdependency of `ssh2-sftp-client`.

Fork the `ssh2` library and [apply a fix](https://github.com/solvenium/ssh2/commit/c12bc4b87f5bfa7d1153c5a66ef645212d6ac0f9).

![ssh2-ident-re-fix](https://user-images.githubusercontent.com/2964543/226106022-c6008b7b-f803-484c-adf6-68bd1f61a166.png)


Override the `ssh2` subdependency in `package.json` to use the forked version:

```json
"overrides": {
    "ssh2": "solvenium/ssh2"
}
```

Problem solved.
