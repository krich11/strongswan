# mbedTLS Plugin Implementation Plan

## Overview
This plan outlines the integration of the **mbedTLS** cryptographic library into StrongSwan. mbedTLS is a lightweight, open-source (Apache 2.0) crypto library that serves as an excellent alternative to OpenSSL, particularly for embedded systems or where a permissive license is required.

## Affected Files
### Configuration
-   `configure.ac`: Add `--enable-mbedtls` option and library checks.
-   `src/libstrongswan/Makefile.am`: Register the new plugin subdirectory.

### New Plugin Directory: `src/libstrongswan/plugins/mbedtls/`
-   `Makefile.am`: Build instructions for the plugin.
-   `mbedtls_plugin.h`: Plugin header.
-   `mbedtls_plugin.c`: Plugin registration and initialization.
-   `mbedtls_crypter.h` / `mbedtls_crypter.c`: Wrapper for mbedTLS symmetric ciphers (AES, etc.).
-   `mbedtls_hasher.h` / `mbedtls_hasher.c`: Wrapper for hashing algorithms (SHA1, SHA2).
-   `mbedtls_rng.h` / `mbedtls_rng.c`: Wrapper for Random Number Generation.
-   `mbedtls_rsa.h` / `mbedtls_rsa.c`: RSA public key operations.
-   `mbedtls_ecc.h` / `mbedtls_ecc.c`: Elliptic Curve operations (optional).

## Design Notes
1.  **Plugin Interface**: Implement the `plugin_t` interface. Register features using `plugin_feature_create`.
2.  **Crypter Interface**: Map StrongSwan's `crypter_t` to mbedTLS's `mbedtls_cipher_context_t`.
    -   Support `ENCR_AES_CBC`, `ENCR_AES_CTR`, etc.
3.  **Hasher Interface**: Map `hasher_t` to `mbedtls_md_context_t`.
    -   Support `HASH_SHA1`, `HASH_SHA256`, etc.
4.  **Dependencies**:
    -   Link against `libmbedtls`, `libmbedcrypto`, `libmbedx509`.

## Step-by-Step Plan
1.  **Build System Setup**:
    -   Update `configure.ac` to check for `mbedtls` headers and libraries.
    -   Add `mbedtls` to the list of plugins in `configure.ac`.
2.  **Plugin Scaffold**:
    -   Create the `mbedtls` directory.
    -   Implement `mbedtls_plugin.c` with a basic `get_name` and `get_features` returning empty for now.
    -   Verify the plugin compiles and loads (even if empty).
3.  **Implement Hashing**:
    -   Create `mbedtls_hasher.c`.
    -   Implement `create` function using `mbedtls_md_info_from_type`.
    -   Register hashing algorithms in `mbedtls_plugin.c`.
4.  **Implement Encryption**:
    -   Create `mbedtls_crypter.c`.
    -   Implement `create` function using `mbedtls_cipher_info_from_type`.
    -   Register encryption algorithms in `mbedtls_plugin.c`.
5.  **Implement RNG**:
    -   Create `mbedtls_rng.c`.
    -   Connect `mbedtls_ctr_drbg` to StrongSwan's RNG interface.
6.  **Testing**:
    -   Enable the plugin.
    -   Run `make check` to see if mbedTLS passes the algorithm tests.
