# Install Management Center on SSP Appliance (Offline)
_Last Update: 16 June 2025_

## [1] Licensing and Image ID
* Prepare the license for Management Center (MC) and download the required image.
* Image is in `.bcsi` file format.

See:
* [Where to get license and download files](https://knowledge.broadcom.com/external/article?articleId=145804)

## [2] Setup HTTP Server
* Ensure host device is in same network as SSP appliance.
* Use Python http.server or VSCode Live Server extension.

## [3] Install Image ID on SSP appliance
* Enter the following commands on SSP CLI
  ```
    config
    images
    load http://10.10.103.36:5500/bccm_3_3-294332.bcsi
  ```

See:
* [ISG CLI Reference](https://broadcom-stage.adobecqms.net/content/dam/broadcom/techdocs/symantec-security-software/web-and-network-security/integrated-secure-gateway/generated-pdfs/ISG-2_x-CLI-Ref.pdf)

## [4] Configure the MC on SSP Appliance
* Enter the following commands on SSP CLI
  ```
    config
    applications
    create <app-type> image-id <app-image> model <app-model> license-id <id> <app-name>
  ```

See: 
* [Which model to use?](https://techdocs.broadcom.com/us/en/symantec-security-software/web-and-network-security/integrated-secure-gateway/2-5/About-ISG/manage-applications/Platform-and-Performance-Reference.html)
* [SSP Setup Guide](https://techdocs.broadcom.com/content/dam/broadcom/techdocs/symantec-security-software/web-and-network-security/hardware-appliances/generated-pdfs/SSP-S210_QSG_231-20004-01.pdf)

## [5] Start the MC application and access MC CLI
* Enter the following commands on SSP CLI
    ```
    config
    applications
    start ManagementCenter
    attach-console ManagementCenter
    ```

## [6] Run Configuration Wizard and Regenerate Default Certificate
* Refer to https://techdocs.broadcom.com/us/en/symantec-security-software/web-and-network-security/management-center/3-3/MC-initial-configuration/before_begin/config_VA.html
* Exit the MC CLI

## [7] Restart MC
```
config
applications
restart ManagementCenter
```

## [8] Access MC
* Wait for several minutes for MC to start up and HTTPS console to be available