# Configure AWS Application Load Balancer without using TLS Passthrough for Jamf ADCS Connector

This guide aims to assist in deploying the Jamf AD CS connector behind an application load balancer in AWS, detailing steps to export the server certificate from the IIS server created by the ADCS Connector installation tool and import it into AWS ACM for use on the load balancer.

## Architecture Overview

<img width="1060" alt="Architecture Overview" src="https://github.com/user-attachments/assets/b397ab53-3497-4273-a841-51f002e4853a" />

**Note**: This guide does not include configuring or deploying an AWS load balancer.

### Requirements:
- Access to the AD CS connector host (IIS server)
- Knowledge of the installation location on the IIS server
- Access to AWS to create ACM certificates

## Uploading to AWS

For a reverse proxy/ALB setup, it's necessary to upload the same server cert used by IIS to ACM.

## Identifying the Server Certificate

1. Log into the AD CS connector Windows host.
2. Open IIS Manager. You can use `Windows + R` and type `inetmgr`, or search for IIS.
3. In the left pane, expand the site (probably the only one), click **Sites**, then select **AdcsProxy**.
4. In the right pane, under Actions, click on **Bindings**.

<img width="680" alt="Identifying the Server Certificate" src="https://github.com/user-attachments/assets/8239cca3-3ecd-490d-ba1e-8879d3d8a4a5" />


5. Select the HTTPS binding, click **Edit**, then click **View** next to the SSL Certificate to see the certificate details.

<img width="867" alt="Identifying Certs Step 7" src="https://github.com/user-attachments/assets/0b2dbf67-4880-4ea6-9261-82a47fbb3258" />


## Exporting the Server Certificate

1. Open `mmc` (Windows + R and type `mmc`).
2. Go to **File → Add/Remove Snap-In**.

<img width="335" alt="Exporting the Server Certificate" src="https://github.com/user-attachments/assets/31714ddd-b60d-4fdf-9ed1-37750bcd5706" />

3. Select **Certificates** and click **Add**.

<img width="428" alt="Exporting the Server Certificate step 3" src="https://github.com/user-attachments/assets/e851b946-efc5-429a-986e-148e29b5ce65" />

4. Choose **Computer Account**, click **Next**, then select **Local Computer** and click **Finish** and then **OK**.

<img width="741" alt="Exporting the Server Certificate step 4" src="https://github.com/user-attachments/assets/68c04b0e-7459-4364-8366-1b0d57c9e2af" />

5. Expand **Certificates (Local Computer)** in the left pane.
6. Navigate to **Personal → Certificates**.
7. Locate the certificate (usually the fqdn of the ADCS connector host), right-click, select **All Tasks → Export**.

<img width="599" alt="Exporting the Server Certificate step 7" src="https://github.com/user-attachments/assets/6816a6d6-ec2f-433d-b0fa-6ef1c7afb078" />

8. In the Certificate Export Wizard, select Yes, export the private key and click **Next**.

<img width="319" alt="Exporting the Server Certificate step 9" src="https://github.com/user-attachments/assets/9a55d07c-da8e-496a-be68-0fb0cca3e4a7" />

9. Select Export all extended properties and click **Next**.

<img width="283" alt="Exporting the Server Certificate step 10" src="https://github.com/user-attachments/assets/f149f2f6-4fbd-4482-8b98-fc80fa476df7" />

10. Select Password and enter a password to use to create the export. You will
need it later, so make sure its something you can remember.

<img width="288" alt="Exporting the Server Certificate step 11" src="https://github.com/user-attachments/assets/f3a5c21b-eb4b-4968-9f32-158b991879ff" />

11. Choose a location to save the exported pfx file, and click **Next**.

12. Review the information and click **Finish**.

<img width="297" alt="Exporting the Server Certificate step 13" src="https://github.com/user-attachments/assets/0bdebc08-f606-4b0e-9818-e256a77b7bb9" />



## Converting the pfx for use with AWS ACM

1. Export the key. It will ask you for a password—this was the password we set in a previous step above, and we will remove it in the next step:
   - `openssl pkcs12 -in adcs-server.pfx -nocerts -out adcs-server.key`
2. Remove the password from the key file (you will be prompted):
   - `openssl rsa -in adcs-server.key -out adcs-server-nopass.key`
   - mv adcs-server-nopass.key adcs-server.key
3. Export the cert:
   - `openssl pkcs12 -in adcs-server.key -clcerts -nokeys -out adcs-server.crt`
4. Make sure you also have the adcs-proxy-ca.cer file from earlier steps. If you do
not have it, you can get it from the Jamf AD CS host located in the location where you ran the .\deploy.ps1 installer, e.g. C:\JAMF\adcs-connector-1.1.0\ADCS Connector\adcs-proxy-ca.cer
5. Convert the chain cer to pem:
   - `openssl x509 -inform der -in adcs-proxy-ca.cer -out adcs-proxy-ca.pem`
6. Now that you have a key, cert, and chain, you can use upload them to ACM, and associate the uploaded ACM cert with your load balancer!

   <img width="307" alt="Converting the pfx for use with AWS ACM step 6" src="https://github.com/user-attachments/assets/6953c9a6-c172-432f-953d-7ba4253b5932" />


## Load Balancer Health Checks

To ensure health checks pass without the client cert:
1. Open IIS Manager, select the site.
2. Double Click on **SSL Settings**.
3. Uncheck **Require SSL**. Restart the site afterwards.

## Troubleshooting

Check if the load balancer is configured correctly:
- **ELB Security Policy** should support TLS 1.2 as Jamf's AD CS connector does not support TLS 1.3.
- **Security Group Ports** should allow communication as needed.

If AWS ACM shows "Key does not match certificate," ensure the correct exporting and splitting of the server cert.

## Additional Resources

- [Installing the Jamf AD CS Connector](https://learn.jamf.com/en-US/bundle/technical-paper-integrating-ad-cs-current/page/Installing_the_Jamf_AD_CS_Connector.html)
- [Importing certificates into AWS Certificate Manager](https://docs.aws.amazon.com/acm/latest/userguide/import-certificate.html)

