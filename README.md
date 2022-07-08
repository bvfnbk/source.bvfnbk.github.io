# bvfnbk.github.io (Sources)

...

## Notes

### Layout

It (appears to be) is possible to _overwrite_ theme-specific files by recreating files with the same name in the
site `layout/` directory.

#### Shortcodes

##### Include Markdown

This shortcode can be used to _include_ other _Markdown_ files in posts. This may be useful to include a common series
overview in a sequence of posts.

**Definition:** `layouts/shortcode/include.html`

**Syntax:**

```markdown
{{% include "$PATH" %}}
```

**Please note:**

* _Hugo_ must be able to find the file using the `$PATH`.
* The `$PATH` _must be_ quoted.
* The development server does not reload the file if it is changed.

### Frontmatter

#### Custom Properties

| Property            | Type    | Description                                    |
|---------------------|---------|------------------------------------------------|
| `includeInPostList` | Boolean | Include post in the list of posts (on _Home_). |

**Please note:**

* `includeInPostList` is `false` if not specified; shared markdown files which are `include`-ed using the shortcode must
  not contain a frontmatter (which will be rendered as a fenced headline otherwise).
