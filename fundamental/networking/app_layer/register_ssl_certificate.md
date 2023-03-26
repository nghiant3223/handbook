# Get an SSL Certificate in 7 Simple Steps
An SSL certificate: 
- A piece of code on your web server that creates an encrypted connection
- Keeps any data submitted by your website users safe and secure.

Follow below steps to get SSL certificate
## 1. Have the Correct Website Information
- SSL certificates are issued by Certificate Authority (CA). - Need provide the right information required by the CA. This information includes:
    - A Unique IP Address
    - An Accurate WHOIS Record: need to verify that you own the domain name. To do that, it will check the domain’s WHOIS record, if it is outdated, please update it.
    - Business/Organization Validation: If requesting a high-assurance certificate, the certificate authority may check:
        - Government databases to validate your business.
        - Government registration document associated with your business.
## 2. Decide Which SSL Certificate You Need
- There are many different types of SSL certificates, can be categorized based on:
    - Validation level: Domain Validation, Organization Validation, and Extended Validation
    - Secured Domains: Single Domain, Wildcard, and Multi-Domain
- Domain Validation
    - Cheapest and lowest level of validation
    - Just makes sure that your company has control over the domain. 
    - Best suited for small businesses that generally don’t exchange any information with users.
- Organization Validation
    - The medium level of validation. 
    - Checks not only domain ownership, but also details of the organization, such as name and location. 
    - Suited for business websites with forms and lead-capturing features.
- Extended Validation
    - The most expensive and thorough level of validation.
    - As well as domain ownership and organization details, it verifies the company’s physical location and legal existence.
    - Suited for websites that handle sensitive information, such as financial transactions.
- Single Domain
    - Protection for a single subdomain. An SSL certificate purchased for `johndoe.com`, for instance, cannot be used for subdomains, such as `blog.johndoe.com`
- Wildcard
    - Protection for unlimited subdomains of a single domain. For example, an SSL certificate purchased for `johndoe.com` can be applied to any subdomains, such as `blog.johndoe.com` or `shop.johndoe.com`.
- Multi-Domain
    - Protection for up to `100` domains with a single SSL certificate. An SSL certificate purchased for `johndoe.com`, for example, can be applied to other domains, such as `janedoe.com`.
## 3. Choose a Certificate Authority
- A Certificate Authority (CA) is an entity that issues SSL certificates.
- E.g. GoDaddy and GlobalSign.
- Pick a reputable CA that can provide the type of SSL certificate you need, while also aligning with your budget and business objectives.
## 4. Generate a Certificate Signing Request (CSR)
- Certificate Signing Request
    - A file to be generated on your web server before you request an SSL certificate from a CA.
    - The CA will use the information in this file to issue your SSL certificate.
- The process of generating a CSR depends on the web server and hosting that your website is using.
## 5. Submit the CSR to Your Certificate Authority
- Go to the website of the CA you picked, and purchase the type of SSL certificate you’ll need.
- Submit the CSR file you generated in the previous step.
## 6. Await Validation by Your Certificate Authority
- Depending on the type of SSL certificate you purchase, the CA can take anywhere between `a couple hours` and `a few days` to validate your details, and issue your site’s SSL certificate.
## 7. Install Your SSL Certificate
- Once the CA has processed your SSL certificate request, you can get the certificate.
- The process of installing an SSL certificate depends on the OS (operating system) of the web server on which your site is hosted.
## 8. Can I Get a Free SSL Certificate?
- Most of the top hosting providers, including Bluehost, HostGator, and InMotion, provide you with a free SSL certificate as part of your hosting package.
## Reference
- [How to get ssl certificate](https://www.websitebuilderexpert.com/building-websites/how-to-get-an-ssl-certificate/)