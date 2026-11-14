# Release process

This repository hosts public release documentation and release assets only. Keep it separate from the private source repository at [taalkhalaf/nbrsplayer](https://github.com/taalkhalaf/nbrsplayer). Never push source code, debug symbols, secrets, signing keys, or unrelated build outputs here.

## Release gate

Do not publish an official release until all of the following are confirmed:

- The release version and matching Git tag are approved.
- The Windows Setup installer has been built and tested.
- License and redistribution obligations for libmpv, FFmpeg, and every bundled dependency have been reviewed and met. This repository does not declare a project license.
- The update manifest format, signing and verification process, key identity, and client behavior have been confirmed from the implementation in the private project.
- The installer, manifest, and checksum have passed the validation steps below.

## Prepare a release

1. **Choose and verify the version.** Use the approved semantic version (for example, `1.0.0`) consistently in the application, installer metadata, release title, and Git tag (for example, `v1.0.0`). Confirm the tag points to the reviewed source commit in the private repository. Do not publish source code here.
2. **Build the Setup installer.** Produce the intended Windows installer only. Do not create or upload a portable package. Record its exact filename and SHA-256 digest.
3. **Prepare release notes.** Start from [RELEASE_NOTES_TEMPLATE.md](RELEASE_NOTES_TEMPLATE.md). Include only changes present in the build.
4. **Generate the signed update metadata using the application's existing implementation.** Follow the documented procedure and exact format supplied by the private project's Codex work. Do not invent a schema, signing algorithm, key format, command, or verification behavior. Never put a private signing key in this repository, in a release asset, in source control, or in an issue.
5. **Generate the SHA-256 checksum file** using the exact filename and format expected by the release procedure. It should identify the installer by its exact asset filename. If the signed manifest itself also needs a checksum, follow the confirmed implementation.
6. **Validate before upload.**
   - Recompute the installer SHA-256 independently and compare it with the checksum file.
   - Verify the manifest signature using the same public verification material and procedure the application uses.
   - Confirm the manifest refers to this exact version, installer URL/asset, and digest, and has no stale or test values.
   - Verify the Setup installer signature if the build is Authenticode-signed, and confirm its publisher and signature status.
   - Install and launch the Setup build on a supported Windows 10/11 test machine. Check upgrade behavior as applicable.
   - Test that an installed copy of the application detects the intended update, validates its signed metadata, downloads the installer, checks integrity, and handles invalid metadata or failed downloads safely. Do not use real users as the test.
7. **Create a GitHub Release** in this repository for the matching tag, using the release notes. Upload the Setup installer, the signed update metadata required by the application, and the checksum file. These metadata files are release support assets; the only application package is Setup. Do not upload source archives, portable builds, private keys, or unrelated binaries.
8. **Verify the published release.** Confirm the release is associated with the expected tag and is published only after the release gate passes. Re-download the published assets and repeat the digest and signature checks. Run the application's update check against the published metadata.
9. **Record the result** in the private project's progress/changelog documentation, including version, tag, asset names, checks performed, and any follow-up. Do not copy private implementation details or secrets into this public repository.

## Details Codex must provide before this process can be made command-specific

Record these in the private project documentation and use them here without guessing:

- Exact manifest filename, schema/version, required fields, and canonical serialization rules.
- Manifest endpoint/URL strategy and how it changes per release/channel.
- Signing algorithm, signature encoding, key identifier/public key distribution, and the app's verification procedure.
- Secure storage and authorized use of the private signing key; never publish the key.
- Exact installer asset naming convention, manifest URL fields, and checksum file name/format.
- Whether the app verifies only manifest authenticity or also installer digest/signature, and its failure/update fallback behavior.
- Reproducible commands or scripts for generating, signing, verifying, and testing the release metadata.
- Required release asset ordering or publication sequence, especially if the app can observe a partially published release.

Until these details are documented and checked against the implementation, this guide intentionally remains procedural rather than specifying a manifest format or signing commands.
