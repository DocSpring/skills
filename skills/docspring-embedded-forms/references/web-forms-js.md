# Web Forms JS reference

`DocSpring.createSimpleForm(cssSelector, templateId, optionsAndCallbacks)` renders a web form (questions on a page) for
a PDF or HTML template inside the element that `cssSelector` matches. It returns nothing. Source of truth:
https://docspring.com/docs/forms/js-libraries/web-forms-js/ (Markdown: replace the trailing slash with `.md`).

## Options

| Option                        | Default               | What it does                                                                                                                                                                                   |
| ----------------------------- | --------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `templateVersion`             | `null`                | The version to show and submit, such as `"1.2.3"` or `"draft"`. Default: the latest published version, or the draft if none is published.                                                      |
| `defaultData`                 | `{}`                  | Values for the fields, as nested submission JSON. Values the person already typed in this browser win (see SKILL.md, "Saved progress beats prefill").                                          |
| `metadata`                    | `{}`                  | Saved with the submission. In API responses and webhooks, not in the PDF. The browser can change it.                                                                                           |
| `showButtons`                 | `true`                | `false` hides both buttons.                                                                                                                                                                    |
| `showClearButton`             | `true`                | `false` hides the Clear button.                                                                                                                                                                |
| `showSubmitButton`            | `true`                | `false` hides the submit button.                                                                                                                                                               |
| `clearButtonLabel`            | `"Clear"`             | The Clear button's text.                                                                                                                                                                       |
| `submitButtonLabel`           | `"Submit Form"`       | The submit button's text.                                                                                                                                                                      |
| `submitButtonSavingLabel`     | `"Saving..."`         | The submit button's text while saving.                                                                                                                                                         |
| `submitButtonProcessingLabel` | `"Generating PDF..."` | The submit button's text while waiting for the PDF.                                                                                                                                            |
| `redirectURL`                 | `null`                | Where to go after submitting. Overrides the template's redirect URL; `null` doesn't clear it.                                                                                                  |
| `waitForPDF`                  | `true`                | `false` stops waiting for the PDF once the form is saved: it redirects straight away (if there's a redirect URL) and doesn't call `onProcessed`. No effect when Submission Privacy is private. |

There is no `test` option: embedded web forms always make live submissions. There is no `region` option either.

## Globals

Set these on `window.DocSpring` before calling `createSimpleForm`:

- `DocSpring.appHost`: the app host, without a scheme. `'app-eu.docspring.com'` for EU accounts; an Enterprise
  install's own host.
- `DocSpring.scheme` (default `'https'`) and `DocSpring.port`: for Enterprise or local installs.

## Callbacks

| Callback                | Arguments              | When                                                                                                                                          |
| ----------------------- | ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| `processTemplateSchema` | `jsonSchema` (mutable) | Before the form renders. Change the schema in place: delete a property (and its entry in `required`) to hide a field.                         |
| `onClearForm`           | none                   | After the Clear button is pressed.                                                                                                            |
| `onSubmit`              | `formData`             | When the submit button is pressed and the form is valid. Return `false` to cancel the submission.                                             |
| `onSave`                | `submission`           | When the submission is saved. Has `id`.                                                                                                       |
| `onProcessed`           | `submission`           | When the PDF is ready. Only when Submission Privacy is public and `waitForPDF` isn't `false`. The redirect, if any, follows one second later. |
| `onError`               | `response`             | On any error (below).                                                                                                                         |

The `submission` object has `id`, `state` (`"pending"`, `"processed"`), `download_url` and `expires_at` (both `null`
until the PDF is processed).

## Errors

When the form can't be saved, `onError` gets the AJAX response: `status`, `statusText`, `responseJSON` (a 422 has
`responseJSON.errors`) and `responseText`. Other errors are objects with an `error` code:

- `failed_request`: the template couldn't be loaded (for example, Online Form Privacy is private), or a request for the
  PDF's status failed. The AJAX response is in `response`.
- `submission_timeout`: the PDF wasn't ready within 60 seconds.
- `invalid_status`: the PDF couldn't be generated. The submission is in `submission`.

```javascript
onError: function (response) {
  if (response.error) {
    console.error(response.error, response.message)
  } else if (response.status === 422) {
    console.error('Validation failed:', response.responseJSON.errors)
  } else {
    console.error('Error:', response.statusText)
  }
}
```

## Redirects

The redirect URL gets `submission_id`, `template_name`, `template_version` and `template_id` query params. With
Submission Privacy private, the form redirects as soon as it saves; with it public, after the PDF is ready (unless
`waitForPDF: false`). Embedded web forms show no confirmation message of their own: use `onSave` or `onProcessed`.

## Styling

The stylesheet only applies inside an element with the class `dsp-form`, so keep that class on the container. The
buttons are `.dsp-form .alpaca-form-button-submit` and `.dsp-form .alpaca-form-button-clear`. Logos set on the account
appear on hosted forms only.

## Hosted web form query params

For links to `https://app.docspring.com/templates/<template ID>/form` (https://docspring.com/docs/forms/web-forms/):

| Param                   | Example                                                                    |
| ----------------------- | -------------------------------------------------------------------------- |
| `data[<field>]`         | `data[name]=John%20Smith`                                                  |
| nested and array fields | `data[person][name]`, `data[person][][name]`                               |
| `metadata[<key>]`       | `metadata[userId]=123`, `metadata[pdf_filename]=Invoice_123`               |
| `hidden_fields`         | `hidden_fields=name,person/0/name` (field names as in the template editor) |
| `version`               | `version=1.2.3`                                                            |
| `mode`                  | `web`, `visual` or `dual`                                                  |

The visual form can't hide fields, so a link with `hidden_fields` shows the web form unless it also has `mode=visual`.
