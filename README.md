# HTML-Email
Emails need to look consistent, but don't nessesarly need to look the [same in every email client](http://doemailshavetolookthesameineveryclient.com/).

## General rules and guidelines
To support emails clients with low level of HTML & CSS (MS Outlook, some Gmail verisons, and mostly non-US clients).
To render your email consistenly with needed code customization track and monitore your audience email client.

- CSS2 instead of CSS3
- Tables instead of Divs
- PNGs instead of SVGs
- Inline CSS instead of embedded styles or stylesheets
- HTML text instead of images containing text

Reference for [HTML](https://www.caniemail.com/) & [CSS](https://www.campaignmonitor.com/css/) support in email.

### HTML and CSS
- Use `<table border="0" cellpadding="0" cellspacing="0" role="article">` when creating new tables.
This negates any unwanted spacing and borders and tells screen readers to skip over the table’s tags and move straight into the content.
- When in doubt, nest another table.
For finer control of your HTML, try nesting tables when building emails.
- Use padding for spacing in table cells.
Margins aren’t fully supported on tables and container elements.
- Use margin for typography.
Margins are fully supported for headline, paragraph, and list tags.
- HTML Attributes
Most styling is done via CSS. But because some email clients use antiquated rendering engines, they tend to better understand attributes like `align`, `valign`, `height`, and `width`.
- Use `align` for layout instead of `float`, `d-flex`, or `flexbox`.
Floats aren’t supported in Outlook and the others don’t have good support in general.
- Define color as `#ffffff` instead of `#fff` or `rgb(1,2,3)`.
Six-digit hex is supported in inline CSS as well as HTML attributes like `bgcolor` that are still supported in email.
- Don’t forget about [preview text](https://www.litmus.com/blog/the-little-known-preview-text-hack-you-may-want-to-use-in-every-email).
We can specify the text that appears beneath subject lines in many email clients. If preview text is not included, this space will be populated by the email’s content.

### Images
- Save images as PNG, GIF, or JPG instead of SVG.
SVG has almost no support in email, no matter how it’s referenced (inline, Base64, `.svg`).
- Save images as @2x and scale them down using HTML attributes.
Since SVG isn’t supported, a 20x20 raster image coded like `<img src="40x40-image.png" height="20" width="20">` displays crisply on high-definition screens.

### Accessibility
- Include role="article" on all tables used for layout.
This prevents screen readers from reading aloud the structure of each table cell.
- Use HTML1 semantic tags whenever possible.
Tags like `<p>` and `<h>` allow screen readers to quickly jump from section to section. Tags like `<strong>` and `<em>` give text more importance.
- Include an `alt` attribute on every image.
Be descriptive and use `alt` to help readers “see” the email if images aren’t displayed. Use an empty `alt=""` for images a screen reader should skip (eg. decorative images). Screenreaders will dictate the filename of images without an `alt` attribute (eg. "icon dash checkmark dot png"). [More on alt text](https://stackoverflow.design/content/examples/alt-text/).
- Avoid “Click Here” or “Learn More” link copy.
It helps us avoid spam filters and gives context about the link to folks using screen readers or dictation software. [Copy guidelines for buttons and links](https://stackoverflow.design/content/guidelines/grammar-and-mechanics/).
- Create a plain text version of every email.
It helps to avoid spam filters, some email clients don’t support HTML, and some people just prefer plain text. They also respond better to things like changing font size, family, and color, and work well with screen magnifiers. We don’t have many rules or guidelines for formatting plain text emails, though they typically look something like this. Our email service providers have tools to convert HTML email to plain text.

### Testing
Tools to ensure emails look as they should when they’re sent.

-[**Litmus**](https://litmus.com/) allows us to preview screenshots of our emails across 90+ email clients on multiple devices. [Builder](https://litmus.com/email-builder) and [Checklist](https://litmus.com/email-checklist) are particularly handy for troubleshooting bugs and circulating email code.

## Outlook conditional CSS
Windows Outlook 2003 and above use Microsoft Word as a rendering engine, that can have some rendering issues. Outlook conditional comments add bits of HTML that are only read by the Word-based versions of Outlook.

### basic syntax
MSO (Microsoft Office) tags to add HTML / CSS anywhere in an email template. This code will be ignored by other email clients. Here’s what it looks like:

```
<!--[if mso]>
    <table><tr><td>
        /* Outlook-specific HTML content goes here. */
    </td></tr></table>
<![endif]-->
```
Only Outlook will render this table.

MSO tags can also be used to add styles targeting Outlook ([Outlook supports CSS in the](https://www.campaignmonitor.com/css/style-element/style-in-head/) <head>)

```
<!--[if mso]>
    <style>
        .example-class {
            /* Outlook-specific CSS goes here. */
        }
    </style>
<![endif]-->
```

### Ghost tables
The main way to use MSO tags in the emails is to create “ghost tables” so hybrid emails don’t fall apart in Outlook. Hybrid design uses inline-block, max-width, min-width to stack table columns. Outlook doesn’t support these CSS properties, so we use MSO tags to create “ghost tables” that apply a fixed width just for Outlook.

```
<!--[if mso]>
<table role="presentation" cellspacing="0" cellpadding="0" border="0" width="100%">
<tr>
<td width="340">
<![endif]-->
    <div style="display:inline-block; width:100%; min-width:200px; max-width:340px;">
        Outlook can’t render the CSS in this DIV but other email clients can,
        so wrap this in a ghost table that replicates the DIV’s desktop style.
        In this case, a container 340px wide.
    </div>
<!--[if mso]>
</td>
</tr>
</table>
<![endif]-->
```

Without the ghost table above, Outlook would display the <div> at 100% width.

### To target specific Outlook version
It is best to target all versions of Outlook using `<!--[if mso]>`, but sometimes it's broken in that specific version

| Outlook version(s)    | Code                                       |
| --------------------- | ------------------------------------------ |
| All Windows Outlook   | `<!--[if mso]> your code <![endif]-->`     |
| Outlook 2000	        | `<!--[if mso 9]> your code <![endif]-->`   |
| Outlook 2002	        | `<!--[if mso 10]> your code <![endif]-->`  |
| Outlook 2003	        | `<!--[if mso 11]> your code <![endif]-->`  |
| Outlook 2007	        | `<!--[if mso 12]> your code <![endif]-->`  |
| Outlook 2010	        | `<!--[if mso 14]> your code <![endif]-->`  |
| Outlook 2013	        | `<!--[if mso 15]> your code <![endif]-->`  |
| Outlook 2016	        | `<!--[if mso 16]> your code <![endif]-->`  |

### Conditional logic
Using operators allows for targeting multiple Outlook versions.
| Code | Description              | Example                                                            |
| ---- | ------------------------ | ------------------------------------------------------------------ |
| gt   | greater than             | <!--[if gt mso 14]> Everything above Outlook 2010 <![endif]-->     |
| lt   | less than                | <!--[if lt mso 14]> Everything below Outlook 2010 <![endif]-->     |
| gte  | greater than or equal to | <!--[if gte mso 14]> Outlook 2010 and above <![endif]-->           |
| lte  | less than or equal to	  | <!--[if lte mso 14]> Outlook 2010 and below <![endif]-->           |
| |    | or	                      | <!--[if (mso 12)|(mso 16)]> Outlook 2007 / 2016 only <![endif]-->  |
| !    | not                      | <!--[if !mso]><!--> All Outlooks will ignore this <!--<![endif]--> |