Digital Marketplace content
===========================
YAML definitions of the Digital Marketplace’s procurement frameworks.

The content here is pulled in to the frontend applications to display forms, and is also used to generate the JSON
schemas used by the API for validation (by running the `scripts/generate-schemas.py` script).

<p align="center">
  <img src="images/frameworks-content-triangle.png?raw=true" alt="Diagram showing how frameworks content is used by our apps">
</p>

[Kev Keenoy](https://peoplefinder.cabinetoffice.gov.uk/people/kevin-keenoy) gave a talk about how it works at PyCon UK 2016. You can watch [Kev's PyCon talk on YouTube](https://youtu.be/vdTw2Vqk4t0?t=12m44s).

Terminology
-----------

* Multiquestion: A multi-question is a question which contains a `questions` key that is a list of sub-questions.
  This lets us reuse the same summary tables markup so that each row represents a group of related questions
  (rather than just being limited to one question per row).

  For example, an `address` multiquestion could have the sub-questions `street`, `town` and `postcode`, and those
  fields would be displayed together on the (multi)question page and in the summary table.

Question keys
-------------

* `question` name of the question, displayed in forms and summary tables (required). This field is a [TemplateField](https://github.com/alphagov/digitalmarketplace-content-loader/blob/474d9adce0f422700cbf2dfc8815a7503ab368bc/dmcontent/utils.py#L8).
* `question_advice` a note about the question asked, usually giving some more information about what it means
   and why it is being asked. This field is a [TemplateField](https://github.com/alphagov/digitalmarketplace-content-loader/blob/474d9adce0f422700cbf2dfc8815a7503ab368bc/dmcontent/utils.py#L8).
* `type` type of the question input, used to find the related toolkit form template (required)
* `name` short question name to use in summary tables instead of the full question. Also used to
  generate the URL slug for addressable questions. This field is a [TemplateField](https://github.com/alphagov/digitalmarketplace-content-loader/blob/474d9adce0f422700cbf2dfc8815a7503ab368bc/dmcontent/utils.py#L8).
* `empty_message` a message to display instead of "Answer required" if question wasn't answered
* `hint` hint text to display after the question, eg advice about how to best format you answer. This field is a [TemplateField](https://github.com/alphagov/digitalmarketplace-content-loader/blob/474d9adce0f422700cbf2dfc8815a7503ab368bc/dmcontent/utils.py#L8).
* `optional` if set to `true` makes the question optional
* `options` a list of possible values for the types that support them. Each option consists of:
    * `label` text displayed on the option label (required)
    * `value` value submitted to the server when the option is selected
    * `filter_label` [currently unused] text displayed in the buyer frontend filters list instead of label
    * `description` additional text displayed after the option label (used for `lot` question)
* `validations` a list of validation errors related to the field. Each validation consists of:
    * `name` the error message key that should match the validation error returned by the API (required)
    * `message` text of the message that will be displayed by the frontend app (required)
* `depends` describes the service conditions that must be met for question to be displayed. Right now, only used to list the
  lots the question applies to. Each depend rule consists of:
    * `"on"` service data key name to use for comparison (e.g. "lot" for lots)
    * `being` a list of acceptable values for the key. If service data key value matches one of the values in the `being` the
       question is kept, otherwise the question is removed from the section for the given service
* `limits` a dictionary of validation/type requirements to limit the possible question values. Available fields:
    * `min_value` integer, sets the minimum value for number questions (default 0)
    * `max_value` integer, sets the maximum value for number questions (default 100)
    * `integer_only` boolean, when `true` makes the number question accept only integer values instead of numbers
    * `format` sets JSON schema 'format' property for text questions (eg `email`, `uri`)
* `number_of_items` sets the maximum number of items for list questions (and changes "Add another (N remaining)" list button, defaults to 10)
* `list_item_name` [currently unused] text displayed in the "Add another ..." button and item names by the list-entry inputs
* `assuranceApproach` contains the name of the set of possible assurance answers for the question. Assurance answer sets are
  listed in the supplier frontend.
* `questions` a list of nested questions (only valid for `multiquestion` questions)
* `any_of` groups nested questions for "anyOf" validations and helps us return a helpful validation message (only valid for `multiquestion` questions)
* `fields` a mapping of toolkit form field key to the service data key used for multi-input field types (only valid for `pricing`)
* `max_length_in_words` sets the limit on question value length in words
* `smaller` allows for smaller textboxes
* `unit` a one-character string. The unit for smaller textboxes accepting a unit (ie, pricing or number entry textboxes)
* `unit_in_full` unit name as a string
* `unit_position` position of the `unit` character within the textbox.
    * Permitted values are `after` or `before`
* `before_summary_value` lists additional values that will be added to the question values on the summary page. Can be used for
  mandatory options or adding additional information to the question (only supported by 'checkboxes' questions at the moment)
* `required_value` for boolean questions can enforce that the correct response is provided (eg, you must answer this question "Yes")
* `dynamic_field` defines the field in the filter context that will be used to generate dynamic list questions (eg when passing
  a brief as filter context setting `dynamic_field` to `brief.niceToHaveRequirements` will generate questions for nice-to-have
  requirements) (only valid for `dynamic_list` questions)
* `followup` sets the name of the question that will be required/shown if the current question is answered with "Yes" (only valid
  for `boolean` questions)


Manifest files and section keys
-------------------------------

Manifest files define a tree-like structure for content.  A manifest is a list of sections.

<p align="center">
  <img src="images/frameworks-manifest-file-tree-structure.png?raw=true" alt="Diagram showing how manifest files define structure">
</p>

Each section contains:

* `name` name of the section (required). This field is a [TemplateField](https://github.com/alphagov/digitalmarketplace-content-loader/blob/474d9adce0f422700cbf2dfc8815a7503ab368bc/dmcontent/utils.py#L8).
* `editable` controls whether section allows updates for the questions, boolean value
* `edit_questions` controls whether individual questions can be edited separately (only supported by `multiquestion` questions)
* `description` text to display after the section name. This field is a [TemplateField](https://github.com/alphagov/digitalmarketplace-content-loader/blob/474d9adce0f422700cbf2dfc8815a7503ab368bc/dmcontent/utils.py#L8).
* `summary_page_description` text to display between the summary table heading and the table body
* `step` decides how sections are grouped on (brief) overview pages
* `questions` a list of section questions (required)

Template Fields
---------------

[`TemplateField`](https://github.com/alphagov/digitalmarketplace-content-loader/blob/474d9adce0f422700cbf2dfc8815a7503ab368bc/dmcontent/utils.py#L8) objects are fields which are turned into jinja Templates, rendered
with a view's context, run through a markdown filter, and returned as a Markup string.
These fields therefore allow:

- markdown formatting
- valid jinja syntax
  - (includes passing through variables)

These are the fields that are turned into TemplateFields when the YAML files are loaded:

| ContentSection                    | Question                                           |
|-----------------------------------|----------------------------------------------------|
| [`name`, `description`](https://github.com/alphagov/digitalmarketplace-content-loader/blob/474d9adce0f422700cbf2dfc8815a7503ab368bc/dmcontent/content_loader.py#L123) | [`question`, `name`, `question_advice`, `hint`](https://github.com/alphagov/digitalmarketplace-content-loader/blob/474d9adce0f422700cbf2dfc8815a7503ab368bc/dmcontent/questions.py#L10) |

[Example usage given in content-loader pull request](https://github.com/alphagov/digitalmarketplace-content-loader/pull/8)

*Note:*
*All markdown formatting, HTML tags, or jinja passed in as part of the content of variables will be escaped.*

Development
-----------

A local checkout of the frameworks repo can be shared with locally-running services (i.e. frontend applications)
as follows:

- from this repo, run `node_modules/bower/bin/bower link`
- from each app, run `node_modules/bower/bin/bower link digitalmarketplace-frameworks`

Then, whenever the framework content is changed:

- on each frontend app, rebuild the app's `content` directory to include the new content by running `make frontend_build` (NB this step also required before deployment to PaaS via `cf push`)
- alternatively, run `npm run frontend-build:watch` to automatically rebuild the framework content whenever a framework YML file changes
- generate schemas into the API's `json_schema` directory using the `scripts/generate-schemas.py` script in this repo.

Running the tests
-----------------

The tests check that the YAML files are valid and that they match a schema.

Setup a VirtualEnv
`make virtualenv`

Install dependencies
`make requirements_for_test`

Run the tests
`make test`

Versioning
----------
Releases of this project follow [semantic versioning](http://semver.org/), ie
> Given a version number MAJOR.MINOR.PATCH, increment the:
>
> - MAJOR version when you make incompatible API changes,
> - MINOR version when you add functionality in a backwards-compatible manner, and
> - PATCH version when you make backwards-compatible bug fixes.

To make a new version:
- update `VERSION.txt` with the new version number
- commit this change; the first line of the commit message **must** be in the
  format `Bump version to X.X.X`
- create a pull request for the version bump

When the pull request is merged a Jenkins job will be run to tag the new
version.
