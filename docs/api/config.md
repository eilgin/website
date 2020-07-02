---
id: config
title: config
sidebar_label: config
---

## `config.envVarInstances`

Scan all environment variables that starts with LACONIA_CONFIG and return the
derived values.

Example:

```js
/**
 * Lambda Environment variables:
 * - LACONIA_CONFIG_SOME_SECRET: ssm://path/to/secret
 */
const config = require("@laconia/config");
const laconia = require("@laconia/core");

const app = async (input, { someSecret }) => {
  /* logic */
};

exports.handler = laconia(app).register(config.envVarInstances());
```

### Environment variable convention

Key: `LACONIA_CONFIG_[VARIABLE_NAME]`

Value:

| Prefix         | Format                     | Example                           | Description                                               |
| -------------- | -------------------------- | --------------------------------- | --------------------------------------------------------- |
| ssm            | ssm:[parameter name]       | ssm:/path/to/my/secret            | Retrieves parameters and secrets from AWS SSM             |
| secretsManager | secretsManager:[secret id] | secretsManager:/path/to/my/secret | Retrieves parameters and secrets from AWS Secrets Manager |
| s3             | s3:[bucket]/[key].json     | s3:bucketName/path/to/config.json | Retrieves application config from S3                      |
| boolean        | boolean:[truthy / falsy]   | boolean:off                       | Converts truthy/falsy values to boolean                   |
| integer        | integer:[an integer]       | integer:9000                      | Converts integer values to true JS integers               |
| float          | float:[a float]            | float:1.345                       | Converts float values to true JS floats                   |

With `boolean` config, these are the falsy values which will be converted to
`false`. If none of these values match, it will be converted to `true`:

- `"false"`
- `"null"`
- `"undefined"`
- `"0"`
- `"no"`
- `"n"`
- `"off"`
- `""` (empty value)

With `integer` and `float` config variables, the passed values will be parsed
if possible, otherwise `NaN` will be returned.
