# Translation system
This document is a work in progress.
There are many open questions remaining, but should serve as a roadmap for the direction we wish 
to take the translation system

## Goals
- Simple, flexible, open
- Same workflow regardless of project; NVDA, add-ons, or other projects.

### High level process
- A registered project uses gettext tools to create a *.pot file.
  - The latest version of this file is hosted at a fixed URL.
- On the translation repo, via a cron scheduled GitHub Action, the *.pot files get downloaded,
  merged, and committed (to the translation repo).
- Translators use the translation repo to provide translations.
- On the project repo, via a cron scheduled GitHub Action, the *.po files get downloaded, committed
  to the project repo.
  
### Project repo definition
A project repo is any project that requires translation (in the NVDA ecosystem).
This may be:
- [NVDA](https://github.com/nvaccess/nvda)
- An addon (and refers directly to the maintainers repository).
  - This removes the reliance on the [nvdaAddons forks](https://github.com/nvdaaddons)
  - This still allows for a translation maintainer and an addon maintainer.
    - userX may manage translation via a fork of the AddonY, contributing them back to userZ who is the original author and maintainer of AddonY

### Repositories
- For simplicity the "registration" data and the translation files live in the same repo.
- All repositories will be hosted on GitHub under the NV Access account.
- Some thought will have to be given to converting the SRT repo into a git repo hosted on GitHub
  - See: https://github.com/nvaccess/mrconfig/issues/7
  - Many revisions can be skipped, we don't care about the history of the `.*-newRevisions/.*` files: https://app.assembla.com/spaces/screenReaderTranslations/subversion/source/HEAD/fr/changes-newRevisions/19733
  - Should the history be maintained at all?
    - Could maintain history in a separate branch.
    - Does anyone or will anyone look at the history?
  - The folder structure will likely be different.

### Project registration
Add the project information to a json file in this repository:
- URL of the project. Typically, a GitHub URL. EG Github.com/nvaccess/nvda.
  - This is useful for communicating with the project. Translators can create issues / discussions
    to ask about strings used in the project.
- POT file URL: This should always point to the most recent POT file.
  - For now, using a l10n branch is preferred.
  - Different projects could implement different approaches, the only requirement is a URL containing the most recent POT file.
  - All ideas considered:
    - The project (addon will have a l10n (localization) branch on their.
      GitHub actions will generate a POT file which will be committed back to this branch.
      - This allows the author to have ultimate control (by merging too/from this branch). 
      - Control over when code should become translatable, or incorporate completed translations.
      - Control over which branches should be the source or destination for translations.
      - Docs and guides for this will be available.
      - Example URL: github.com/nvaccess/nvda/tree/l10n/source/locale/nvda.pot
    - Use gitHub actions, save pot file as a workflow artifact?
      - https://github.com/actions/upload-artifact
      - How do we download the pot file? The URL seems to contain a workflow / artifact ID?
      - Doesn't pollute the repo with unnecessary pot files
    - Upload as an artifact for a 'l10n' release?
      - This will create noise on the repositories releases page and imposes quite an opinionated workflow.
  
  

### POT file creation
The PO (Portable Object) Template file, containing the source strings to be translated must be
created by the project.
Projects will be responsible for building this POT file. A GitHub action created that can be used by add-ons to generate the POT file.


### Translators
- A 'translations' repo will host all the po / pot files.
- Tools can be provided to
  - Understand what is new to translate for a particular language.
  - Handle the git commands, including creating the PR.
- Translations contributed back via PR
  - It will run automated checks, auto merged on pass. 
  - Feedback via GitHub's notification/email system if there are errors with the file format etc.
  

### Notes
- The current translation system has support for Txt2Tags (t2t) files.
  These are a source of complexity in the translation system.
  To simplify, all t2t files will be converted to a format that has support for the gettext
  system.
  - See issues:
    - https://github.com/nvaccess/nvda/issues/7303
    - https://github.com/nvaccess/nvda/issues/4371
    - https://github.com/nvaccess/nvda/issues/11044.
  - A strategy to keep old translations of the t2t files will need to be given careful
    consideration. 
