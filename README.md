## About

Portable password hashing framework for PHP. Originally inspired by [phpass v0.3](http://www.openwall.com/phpass/), hence the name PasswordHash2. However, it resembles none of the phpass framework.

If the crypt() does not implement the desired scheme, a pure PHP implementation is available. My proposed SHA2 schemes are, well, proposed. Unless they don't gain some traction, you won't see them implemented into the mainstream libraries.

For more details over the implemented crypt() schemes, follow these links:

 * http://en.wikipedia.org/wiki/Crypt_%28Unix%29#Blowfish-based_scheme
 * https://github.com/SaltwaterC/PasswordHash2/wiki/Proposed-SHA2-crypt%28%29-schemes

## System Requirements

 * PHP 5.3.8+
 * OpenSSL extension
 * hash extension with sha256 and sha512 support

## Advisories

 * [CVE-2011-2483: crypt_blowfish 8-bit character mishandling](http://www.openwall.com/lists/oss-security/2011/06/20/2) - affects the PHP's bcrypt implementation up to 5.3.6. PHP 5.3.7 was short lived due to a bug in CRYPT_MD5 (but it contains a fixed version of bcrypt), therefore the recommendation is 5.3.8. For more details on the crypt() blowfish security issue in pre 5.3.6 see [the crypt blowfish page](http://www.php.net/security/crypt_blowfish.php). PasswordHash2 continues to use the 2a prefix for the bcrypt hashes. You may need to change the existing hashes to 2x and implement a rehashing strategy.

## Reference

### Constants

PasswordHash2::bcrypt, PasswordHash2::sha256, PasswordHash2::sha512

> Constants that have the following values: bcrypt, sha256, sha512. You may pass either the textual value or the constant. Your choice.

### Properties

PasswordHash2::$error

> Array that contains all the class error information in the form errno => error.

### Methods

PasswordHash2::hash($password, $algo = self::bcrypt, $cost = 8)

 * $password - the input password
 * $algo - one of the constant value described above that sets the hashing algorithm
 * $cost - the cost parameter of the hashing scheme. The SHA2 scheme usually names this as rounds in its terminology

> Returns the desired hash on success. An exception on any kind of failure. The cost parameter is truncated to the nearest limit as described by the PHP documentation: 4 - 31 for bcrypt, 1000 - 999999999 for SHA2.

PasswordHash2::check($password, $hash)

> Checks a password against an input hash.

PasswordHash2::bcrypt($password, $cost = 8)

> Alias for PasswordHash2::hash($password, PasswordHash::bcrypt, $cost);

PasswordHash2::sha256($password, $rounds = 5000)

> Alias for PasswordHash2::hash($password, PasswordHash::sha256, $rounds);

PasswordHash2::sha512($password, $rounds = 5000)

> Alias for PasswordHash2::hash($password, PasswordHash::sha512, $rounds);

PasswordHash2::info($hash)

> Returns the information about an input $hash or $salt. The returned array contains the following keys: 'algo' - one of the defined algo constants, 'cost' - the cost / rounds parameter, 'seed' - the 22 base64 chars of random seed. This method is used internally, but you may use it to implement adaptive hashing.

CryptPHP::sha256($password, $seed, $rounds)

> Reference implementation for my sha256 scheme.

CryptPHP::sha512($password, $seed, $rounds)

> Reference implementation for my sha512 scheme.

CryptPHP::base64_decode($string)

> The base64 decoder implemented by bcrypt, in use by my proposed schemes to decode the random seed of the salt.

CryptPHP::base64_encode($string)

> The base64 encoder implemented by bcrypt, internally in use by the salt generator.

## Storage requirements

 * 60 chars for bcrypt hashes
 * 75-80 chars for sha256 hashes (length determined by the rounds parameter)
 * 118-123 chars for sha512 (length determined by the rounds parameter)

## Error reporting

The framework reports the errors by throwing exceptions. They always use the follwing format new Exception($error, $errno);

## Misc

php test.php

> Test script, added for convenience. Yells at you if you don't have the system requirements.

The sha256 crypt() proves to be around 1.8x - 2.3x slower than the native sha256 crypt() implementation.
