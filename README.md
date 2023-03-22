﻿# backend-library

### TOTP (Time-based one-time passwords)
#### Initialization TOTP instance
TOTP can accept more paramaters: `passwordLength`, `period`, `algorithm` and `secret`. The default values are: passwordLength = 6, period = 30 and algorithm = SHA1.

```java
// Generate a secret (or use your own secret)
byte[] secret = SecretGenerator.generate();

TOTPGenerator totp = new TOTPGenerator.Builder(secret)
        .withHOTPGenerator(builder -> {
            builder.withPasswordLength(6);
            builder.withAlgorithm(HMACAlgorithm.SHA1); // SHA256 and SHA512 are also supported
        })
        .withPeriod(Duration.ofSeconds(30))
        .build();
```

Get information about the generator:
```java
byte[] secret = totpGenerator.getSecret();
int passwordLength = totpGenerator.getPasswordLength(); // 6
HMACAlgorithm algorithm = totpGenerator.getAlgorithm(); // HMACAlgorithm.SHA1
Duration period = totpGenerator.getPeriod(); // Duration.ofSeconds(30)
```

#### Generation of TOTP code
After creating an instance of the TOTP class, a code can be generated by using the `now()` method, similarly with the HOTP class:
```java
try {
    String code = totpGenerator.now();
     
    // To verify a token:
    boolean isValid = totpGenerator.verify(code);
} catch (IllegalStateException e) {
    // Handle error
}
```
The above code will generate a time-based one-time password based on the current time. The API supports, besides the current time, the creation of codes based on `timeSince1970` in seconds, `Date`, and `Instant`:

```java
try {
    // Based on current time
    totpGenerator.now();
    
    // Based on specific date
    totpGenerator.at(new Date());
    
    // Based on specific local date
    totpGenerator.at(LocalDate.of(2023, 3, 2));
    
    // Based on seconds past 1970
    totpGenerator.at(9238346823);
    
    // Based on an instant
    totpGenerator.at(Instant.now());
} catch (IllegalStateException e) {
    // Handle error
}
```
