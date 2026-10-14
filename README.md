# retry-storm-k8s-hashes

SHA-256 hashes of files in a private repository, each with RFC 3161 timestamps from two public timestamping authorities, DigiCert and Sectigo. Nothing else is here.

A hash lets anyone later shown the private repository check that a file is the one published here. Its timestamps let them check that the file existed by the time the authorities signed: an authority's timestamp is its signature over the hash and its own time. A commit's date in this repository is the committer's clock and proves nothing by itself; the timestamps do.

```
entries/<label>.sha256             "<sha256>  <the file's path in the private repository>"
entries/<label>.digicert.tsr       DigiCert's RFC 3161 timestamp on that hash
entries/<label>.sectigo.tsr        Sectigo's
```

To check one with OpenSSL, given the file:

```
sha256sum <the file>                                      # must equal the hash in entries/<label>.sha256
openssl ts -reply -in entries/<label>.digicert.tsr -text   # the authority's time, and the hash it signed
openssl ts -verify -digest <that hash> -in entries/<label>.digicert.tsr -CApath /etc/ssl/certs
```

The last must print `Verification: OK`; the same for the `.sectigo.tsr` file. DigiCert's timestamps chain to DigiCert Trusted Root G4 (SHA-256 fingerprint `552F7BDCF1A7AF9E6CE672017F4F12ABF77240C78E761AC203D1D9D20AC89988`), Sectigo's to USERTrust RSA Certification Authority (`E793C9B02FD8AA13E21C31228ACCB08119643B749C898964B1746D46C3D4CBD2`); both are in the usual operating-system root stores.
