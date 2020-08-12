Initial commands
================

[◀ Go back to main README](README.md)

These command are inteneded for initial setup. If you are not aware of the
procedure please follow [the long way in detail](README.md#the-long-way-in-detail).

    {
      / tool fetch "https://git.eworm.de/cgit/routeros-scripts/plain/certs/Let%27s%20Encrypt%20Authority%20X3.pem" dst-path="letsencrypt.pem";
      :delay 1s;
      / certificate import file-name=letsencrypt.pem passphrase="";
      :if ([ / certificate print count-only where fingerprint="96bcec06264976f37460779acf28c5a7cfe8a3c0aae11a8ffcee05c0bddf08c6" or fingerprint="731d3d9cfaa061487a1d71445a42f67df0afca2a6c2d2f98ff7b3ce112b1f568" ] != 2) do={
        :error "Something is wrong with your certificates!";
      }
      / file remove "letsencrypt.pem";
      :foreach Script in={ "global-config"; "global-config-overlay"; "global-functions" } do={
        / system script add name=$Script source=([ / tool fetch check-certificate=yes-without-crl ("https://git.eworm.de/cgit/routeros-scripts/plain/" . $Script) output=user as-value]->"data");
      }
      / system script set comment="ignore" global-config-overlay;
      / system script { run global-config; run global-config-overlay; run global-functions; }
      / system scheduler add name="global-scripts" start-time=startup on-event="/ system script { run global-config; run global-config-overlay; run global-functions; }";
      :global CertificateNameByCN;
      $CertificateNameByCN "ISRG Root X1";
      $CertificateNameByCN "Let's Encrypt Authority X3";
    }

---
[◀ Go back to main README](README.md)  
[▲ Go back to top](#top)
