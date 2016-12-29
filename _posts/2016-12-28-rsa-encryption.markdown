---
layout: post
title:  "Passing Notes with RSA Encryption"
date:   2016-12-29 11:16:29 -0500
categories: rsa encryption
---

### Framing the Problem

RSA encryption is one of the most common ways to securely pass data through the web. Examples include SSL, SSH, digital signatures, and PGP. Writing this post helped me understand it better. Hopefully reading it will do the same for you.

Let's frame the problem first. We are the kingpins of our middle school's secret note passing ring. But, notes could be intercepted by teachers at any moment. Anyone in the school needs to be able to pass us a secret note, but no one else should know how to read it. So we want to an encryption system in which we are the only one who knows how to decrypt but anyone can follow our directions and encrypt a message. That way, even if messages are captured as they travel, the contents of the message will remain secret.

### Set Up The System (Make the Public and Private Keys)

1. Pick two prime numbers `p` and `q`. (I picked small ones since they make the examples easy to understand. In practical uses these primes are enormous.)

    `p = 3`
    `q = 7`

1. Find some products `n` and `t` using those primes.

    ```
    n = p * q = 7 * 3 = 21


    t = (n - 1) * (q - 1) =  6 * 2 = 12

    ```

1. Pick an integer `e` that does not share any factors with `t`.

    I choose `5` since it is small and shares no factors with `12`.

    `e = 5`

1. The tough step! Find an integer `d` such that `d * e mod t = 1`.

    [What is mod?](https://en.wikipedia.org/wiki/Modular_arithmetic)

    So we need to fill in the blank to make this statement true.

    `__ * 5 mod 12 = 1`

    `17` works and is pretty small so let's choose 17.

    `d = 17`

  1. Recap all those numbers we just found.

      ```
      p = 3
      q = 7
      n = 21
      t = 12
      e = 5
      d = 17
      ```


### Publish the Directions (Public Key to Encrypt Message)

Ok we did some math. Now we need to write some directions for everyone. Right now, no one knows how to use our encryption system. So let's spell it out for them so they can start sending us secret notes.

Disclaimer: The contents of a secret note must be a number.

DIRECTIONS
1. Change your message into a number somehow. Let's call your message `M`.
2. Use the formula `C = M^n mod e` to calcuate the encrypted message `C`.
    `M` is your message
    ```
    n = 21
    e = 5
    ```
3. Write `C` on a note and pass it around.

Note: Keep the value of `d` secret.

#### Example

The secret message we want to pass `M` is `10`.

Given that our formula is `C = M^e mod n`, and `e = 5` and `n = 21`.

    ```
    C = 10^5 mod 21 = 19
    ```

So the content of the secret note `C` is `19`.


### Read the Messages (Private Key To Decrypt Message)

Finally the secret notes are coming in. Let's assume everyone followed our directions when encrypting the message. To decrypt we use our private key, `n` and `d`. We will plug in the value of the secret message `C` into this formula to uncover the original secret message `M`.

```
M = C^d mod n
```

#### Example

The encrypted message `C` is `19` (from the example above).

To find the original message `M`, we use the formula `M = C^d mod n`, with `d = 17` and `n = 21`.

    M = 19^17 mod 21 = 10

We found `10`, the same number we started with!

### Sum it Up

Although middle school kids are not lining up to use RSA encryption to pass secret notes, RSA encryption is used extensively to transmit private data over the Internet. The public key `n` and `e` can be published and the private key `n` and `d` is nearly impossible to find when the starting primes `p` and `q` are large enough. This creates a useful one-way information track where anyone can encrypt but only the server-side with the private key can decrypt.




