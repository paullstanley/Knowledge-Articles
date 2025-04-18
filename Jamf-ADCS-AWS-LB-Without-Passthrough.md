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
3. Select **Certificates** and click **Add**.
4. Choose **Computer Account**, click **Next**, then select **Local Computer** and click **Finish** and then **OK**.
5. Expand **Certificates (Local Computer)** in the left pane.
6. Navigate to **Personal → Certificates**.
7. Locate the certificate (usually the fqdn of the ADCS connector host), right-click, select **All Tasks → Export**.
8. Follow the wizard to export the certificate with the private key and all extended properties. Set a memorable password as you'll need it later.

## Converting the pfx for use with AWS ACM

1. Convert the server certificate from pfx to pem format using OpenSSL commands:
   - Export the key and remove the password.
   - Export the cert.
   - Convert the chain cer to pem.
2. Upload them to ACM and associate the ACM cert with your load balancer.

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

