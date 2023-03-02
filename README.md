# strapi-provider-upload-aws-cloudfront-fix

## Resources

- [LICENSE](LICENSE)

## Links

- [Strapi website](https://strapi.io/)
- [Strapi documentation](https://docs.strapi.io)
- [Strapi community on Discord](https://discord.strapi.io)
- [Strapi news on Twitter](https://twitter.com/strapijs)
- [Fix by TheBit Research](https://thebitresearch.com)

## Installation

```bash
# using yarn
yarn add strapi-provider-upload-aws-cloudfront-fix

# using npm
npm install strapi-provider-upload-aws-cloudfront-fix --save
```

## Configurations

Your configuration is passed down to the provider. (e.g: `new AWS.S3(config)`). You can see the complete list of options [here](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/S3.html#constructor-property)

See the [using a provider](https://docs.strapi.io/developer-docs/latest/plugins/upload.html#using-a-provider) documentation for information on installing and using a provider. And see the [environment variables](https://docs.strapi.io/developer-docs/latest/setup-deployment-guides/configurations/optional/environment.html#environment-variables) for setting and using environment variables in your configs.

### Provider Configuration

`./config/plugins.js`

```js
module.exports = ({ env }) => ({
  // ...
  upload: {
    config: {
      provider: 'strapi-provider-upload-aws-cloudfront-fix',
      providerOptions: {
        accessKeyId: env('AWS_ACCESS_KEY_ID'),
        secretAccessKey: env('AWS_ACCESS_SECRET'),
        region: env('AWS_REGION'),
        params: {
          Bucket: env('AWS_BUCKET'),
        },
        cdn: env('AWS_CLOUDFRONT')
      },
    },
  },
  // ...
});
```

### Security Middleware Configuration

Due to the default settings in the Strapi Security Middleware you will need to modify the `contentSecurityPolicy` settings to properly see thumbnail previews in the Media Library. You should replace `strapi::security` string with the object bellow instead as explained in the [middleware configuration](https://docs.strapi.io/developer-docs/latest/setup-deployment-guides/configurations/required/middlewares.html#loading-order) documentation.

`./config/middlewares.js`

```js
module.exports = [
  // ...
  {
    name: 'strapi::security',
    config: {
      contentSecurityPolicy: {
        useDefaults: true,
        directives: {
          'connect-src': ["'self'", 'https:'],
          'img-src': ["'self'", 'data:', 'blob:', `${process.env.AWS_CLOUDFRONT}`],
          'media-src': ["'self'", 'data:', 'blob:', `${process.env.AWS_CLOUDFRONT}`],
          upgradeInsecureRequests: null,
        },
      },
    },
  },
  // ...
];
```

#Add the required environment variables to your .env file

```
AWS_ACCESS_KEY_ID=
AWS_ACCESS_SECRET=
AWS_REGION=
AWS_BUCKET=
AWS_CLOUDFRONT=
```


## Required AWS Policy Actions

These are the minimum amount of permissions needed for this provider to work.

```json
"Action": [
  "s3:PutObject",
  "s3:GetObject"
],
```


