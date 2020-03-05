<p align='center'><br><a href='https://thenounproject.com/term/components/1286164'><img src='https://user-images.githubusercontent.com/74385/75975632-f9100d80-5f2c-11ea-8c20-72a98b8de15e.png' width='96'></a><br></p>

<h1 align='center'>
gatsby-remark-component
</h1>

<p align='center'>
Embed components in React inside <em>div</em> wrappers.
</p>

<p align='center'>
<a href='https://npmjs.com/package/@rstacruz/gatsby-remark-component'>
<img src='https://badge.fury.io/js/%40rstacruz%2Fgatsby-remark-component.svg' alt='npm version'>
</a>
</p>
<br>

A gatsby-transformer-remark plugin that change the AST node parent of a custom component to a div. This is an unofficial maintenance fork of [Hebilicious/gatsby-remark-component](https://github.com/Hebilicious/gatsby-remark-component) continued from its v1.1.3.

## Install

```bash
yarn add gatsby-transformer-remark @rstacruz/gatsby-remark-component
```

## Usage

> Read the great custom component article on the official gatsby remark blog [here](https://using-remark.gatsbyjs.org/custom-components/).

This is the base settings, your components inside your markdown will be auto-detected.

```js
// gatsby-config.js
plugins: [
  {
    resolve: "gatsby-transformer-remark",
    options: {
      plugins: ["@rstacruz/gatsby-remark-component"]
    }
  }
]
```

## Configuration

You can explicitly declare the name of the components if you want to be strict.

```js
plugins: [
  {
    resolve: "gatsby-transformer-remark",
    options: {
      plugins: [
        {
          resolve: "@rstacruz/gatsby-remark-component",
          options: { components: ["my-component", "other-component"] }
        }
      ]
    }
  }
]
```

## How it works

When you start Gatsby, your queries will be built from your components, and gatsby-remark-components will update the AST tree.

<details>
<summary>Technical details...</summary>

This will convert this graphql result:

```json
//...
{
  "type": "element",
  "tagName": "p",
  "properties": {},
  "children": [
    {
      "type": "element",
      "tagName": "my-component",
      "properties": {},
      "children": []
    }
  ]
}
```

To this:

```json
//...
//Notice the tag name
{
  "type": "element",
  "tagName": "div",
  "properties": {},
  "children": [
    {
      "type": "element",
      "tagName": "my-component",
      "properties": {},
      "children": []
    }
  ]
}
```

Now in your markdown template you can do:

```jsx
import rehypeReact from "rehype-react"
import { MyComponent } from "../pages/my-component"

const renderAst = new rehypeReact({
  createElement: React.createElement,
  components: { "my-component": MyComponent }
}).Compiler
```

Replace :

```jsx
<div dangerouslySetInnerHTML={{ __html: post.html }} />
```

by:

```jsx
<div>{renderAst(post.htmlAst)}</div>
```

And in your page query ... :

```jsx
//...
markdownRemark(fields: { slug: { eq: $slug } }) {
 htmlAst // previously `html`

 //other fields...
}
//...
```

Now in your markdown you can do:

```md
# Some Title

Some text

<my-component></my-component>
```

This will render your component without any validateDOMNesting warning.

</details>
