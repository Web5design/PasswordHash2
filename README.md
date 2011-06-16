## About

Password Hashing class, originally inspired by phpass v0.3 http://www.openwall.com/phpass/

Unlike the phpass, PasswordHash2 does not fallback when the desired hashing method is unavailable. It also uses a consistent random seed mechanism for generating the salt aka unlike phpass it does not use arcane methods for getting a bunch of random bytes. It uses openssl_random_pseudo_bytes() behind the scenes. You don't have to generate the salt by yourself. The hash method does all the heavy lifting as well as the boundary checking for the cost (rounds) parameter. Fails gracefully by returning FALSE if the algo is missing or the resulted hash length is unexpected.

Since v0.2 this implementation isn't compatible with the hashes generated by phpass and crypt() schemes as it stores the string as base64 encoded due to the implementation of the SHA2-based scheme with 16 bytes of random seed for salt.

Since v0.2.2, there's an alternative short version for storing the hashes, as well incompatible with the crypt() schemes, but shorter than the crypt() returned value.

For more details over the implemented crypt() schemes, follow these links:

 - http://en.wikipedia.org/wiki/Crypt_%28Unix%29#Blowfish-based_scheme
 - http://en.wikipedia.org/wiki/Crypt_%28Unix%29#SHA-based_scheme

The previous statements imply certain system requirements.

## System Requirements

 * PHP 5.3.0+ (bcrypt) or PHP 5.3.2+ (all methods)
 * OpenSSL extension

This implementation works consistently across platforms. There are no Windows-isms or *nix-isms in this implementation. In fact, this is hack-free from the PHP implementation point of view.

## Class Reference

### Constants

PasswordHash2::bcrypt, PasswordHash2::sha256, PasswordHash2::sha512

> They have the same textual value as the previous (> v0.2.1) bcrypt, sha256, and sha512 values that you needed to pass to the hash() method. Since the use of constants avoids typo errors (given the appropriate IDE), their use is recommended. However, the existing code is backward compatible.

### Methods

PasswordHash2::hash($password, $algo = self::bcrypt, $cost = 8, $short = FALSE)

 * $password - the input password
 * $algo - one of the constants described above that sets the hashing algorithm. You may pass the text values of the constants, but this is not recommended.
 * $cost - the cost parameter of the hashing scheme. The SHA-2 scheme usually names this as 'rounds' in its terminology
 * $short - makes the hash() method to return a short version of the hash, unlike the standard base64 encoded output. The short flag has the same usage mode for all the methods that features it. This new flag does not break the compatibility with the previous v0.2 releases.

> Returns the desired hash on success, FALSE on failure / lack of proper support for the desired scheme. Implementing the system requirements check ar every run is just useless overhead. It should not fail unless you don't have the required PHP version and the OpenSSL extension. Raises an E_USER_WARNING if the OpenSSL random seed is not considered to be crypto strong. Unless your setup is really broken, this should not occur. The returned hash is base64 encoded due to the fact that the SHA2-based scheme uses 16 bytes of random seed for salt, *NOT* 16 chars as stated by PHP's crypt() documentation. The base64 encoding makes it ASCII friendly at the cost of 33% more used space for storing a hash. The short version is well, shorter, shorter than the crypt() returned value, but incompatible with the standard scheme. Conversion methods are provied by the PasswordHash2 class. The cost parameter is truncated to the nearest limit as described by the PHP documentation: 4 - 31 for bcrypt, 1000 - 999999999 for SHA-2.

PasswordHash2::shorten($hash, $raw = TRUE)

 * $hash - hash to be shorten
 * $raw - by default it assumes that the hash is returned by crypt(), otherwise it makes a base64_decode() of the input

PasswordHash2::check($password, $hash, $short = FALSE)

> Checks a password against an input hash.

PasswordHash2::expand($shorthash)

> Expands a short hash to the original crypt() representation.

PasswordHash2::rehash($password, $hash, $algo = self::bcrypt, $cost = 10, $short = FALSE)

> check() + hash() wrapper for making simple to implement rehashing, but pretty inefficient in the long run if the user database is large. Returns the new hash on success, FALSE on any kind of failure.

PasswordHash2::cost($hash, $short = FALSE)

> Returns the cost / rounds value of an input hash. Makes possible to implement efficient rehashing strategies at the cost of more added logic into the application.

PasswordHash2::bcrypt($password, $cost = 8, $short = FALSE)

> Alias for PasswordHash2::hash($password, PasswordHash::bcrypt, [...]);

PasswordHash2::sha256($password, $rounds = 5000, $short = FALSE)

> Alias for PasswordHash2::hash($password, PasswordHash::sha256, [...]);

PasswordHash2::sha512($password, $rounds = 5000, $short = FALSE)

> Alias for PasswordHash2::hash($password, PasswordHash::sha512, [...]);

## Misc

php test.php

> Test script, added for convenience. Yells at you if you don't have the system requirements.

