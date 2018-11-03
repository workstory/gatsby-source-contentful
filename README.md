# gatsby-source-contentful

Source plugin for pulling content types, entries, and assets into Gatsby from
Contentful spaces. It creates links between entry types and asset so they can be
queried in Gatsby using GraphQL.

An example site for using this plugin is at
https://using-contentful.gatsbyjs.org/

## Install

`npm install --save gatsby-source-contentful`

## How to use

First, you need a way to pass environment variables to the build process, so secrets and other secured data aren't committed to source control. We recommend using [`dotenv`][dotenv] which will then expose environment variables. [Read more about dotenv and using environment variables here][envvars]. Then we can _use_ these environment variables and configure our plugin.

### Using Delivery API

```javascript
// In your gatsby-config.js
module.exports = {
  plugins: [
    {
      resolve: `gatsby-source-contentful`,
      options: {
        spaceId: `your_space_id`,
        // Learn about environment variables: https://gatsby.app/env-vars
        accessToken: process.env.CONTENTFUL_ACCESS_TOKEN,
      },
    },
  ],
}
```

### Using Preview API

```javascript
// In your gatsby-config.js
module.exports = {
  plugins: [
    {
      resolve: `gatsby-source-contentful`,
      options: {
        spaceId: `your_space_id`,
        // Learn about environment variables: https://gatsby.app/env-vars
        accessToken: process.env.CONTENTFUL_ACCESS_TOKEN,
        host: `preview.contentful.com`,
      },
    },
  ],
}
```

### Offline

If you don't have internet connection you can add `export GATSBY_CONTENTFUL_OFFLINE=true` to tell the plugin to fallback to the cached data, if there is any.

### Configuration options

**`spaceId`** [string][required]

Contentful spaceId

**`accessToken`** [string][required]

Contentful delivery api key, when using the Preview API use your Preview API key

**`host`** [string][optional] [default: `'cdn.contentful.com'`]

The base host for all the API requests, by default it's 'cdn.contentful.com', if you want to use the Preview API set it to `'preview.contentful.com'`. You can use your own host for debugging/testing purposes as long as you respect the same Contentful JSON structure.

**`environment`** [string][optional] [default: 'master']

The environment to pull the content from, for more info on environments check out this [Guide](https://www.contentful.com/developers/docs/concepts/multiple-environments/).

You can pass in any other options available in the [contentful.js SDK](https://github.com/contentful/contentful.js#configuration).

## Notes on Contentful Content Models

There are currently some things to keep in mind when building your content models at contentful.

1.  At the moment, Fields that do not have at least 1 populated instance will not be created in the graphql schema.

2.  When using reference fields, be aware that this source plugin will automatically create the reverse reference. You do not need to create references on both content types. For simplicity, it is easier to put the reference field on the child in child/parent relationships.

## How to query

Two standard data types will be available from Contentful: `ContentType` and
`Asset`.

You can query Asset nodes created from Contentful like the following:

```graphql
{
  allContentfulAsset {
    edges {
      node {
        id
        file {
          url
        }
      }
    }
  }
}
```

Non-standard data types, i.e. entry types you define in Contentful, will also be
available in Gatsby. They'll be created in your site's GraphQL schema under
`contentful${entryTypeName}` and `allContentful${entryTypeName}`. For example,
if you have `Product` as one of your content types, you will be able to query it
like the following:

```graphql
{
  allContentfulProduct {
    edges {
      node {
        id
        productName
        image {
          fixed(width: 100) {
            width
            height
            src
            srcSet
          }
        }
      }
    }
  }
}
```

## **Beta** [Contentful Rich Text](https://www.contentful.com/developers/docs/concepts/rich-text/)

If you want to use the new Rich Text feature you can opt-in by setting the following environment variable:

```sh
export GATSBY_CONTENTFUL_RICH_TEXT='enabled'
```

[dotenv]: https://github.com/motdotla/dotenv
[envvars]: https://gatsby.app/env-vars
