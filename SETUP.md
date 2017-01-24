## Making MkDocs happy

### You must always:

- Include an `index.md` file at the root folder of your documentation. (Note: [MkDocs will allow this to be customized in the future.](https://github.com/mkdocs/mkdocs/issues/608))
- Include all local documentation assets, such as images, inside this root folder (or link to an external source).
- Start each page with a top-level heading. (Note: The level of heading should no longer affect presentation, but I still need to test whether this affects TOC creation.)

### Markdown formatting tweaks for compatibility with GitHub

- Blank lines between different blocks of content will be your best friend(s). So, include a blank line before and after bulleted lists, numbered lists, code blocks, images...
- If a code block or image is supposed to be part of a list, remember the blank lines before and after, and _also_ indent it **four spaces**. Using or mixing tabs might cause problems. Python Markdown is a lot pickier about this than GitHub-flavored Markdown, causing lists to nest improperly or break numbering altogether.
- Good luck!

## Updating documentation sources

There are two things to do if you want to change the GitHub source of documentation.

1. **Update the project configuration file.** This is located at `config/project-name.yml` file. Look for the `extra` key and find or create, one level in, the `docs_base_url` key. This is used to build the "edit in GitHub" links at the bottom of each page. The actual path and file name are appended to the base URL. It will look something like this:

    ```yml
    extra:
      docs_base_url: https://github.com/mapzen/mapzen-docs/tree/master/metro-extracts
    ```

2. **Update the repository path in the Makefile.** The Makefile is located in this repo's root, and is called `Makefile`. This step is a little harder and benefits from some knowledge of shell scripting and `Make`. Generally, we want to first retrieve the source documentation file, which is available from GitHub inside a pre-packaged archive with the extension `tar.gz`. We locate it by setting a variable with the file's location, which might look something like this:

    `TANGRAM = https://github.com/tangrams/tangram-docs/archive/gh-pages.tar.gz`
    
    Next, we uncompress it, with a line further down in the Makefile that looks something like this:
    
    `curl -sL $(TANGRAM) | tar -zxv -C src-tangram --strip-components=2 tangram-docs-gh-pages/pages`
    
    This will extract the files into the `src/project-name` directory, which makes them available to mkdocs. If you're getting files from the `mapzen-docs` repository, you will have to flatten the directory structure a level up because of how the repository is organized. This step can vary depending on the project, which is why it's not super friendly.

    You can also change the branch used as the source of the documentation with these lines, which can be handy for testing purposes. This is accomplished by replacing `gh-pages` with the name of another branch. Note that you'll need to convert any slashes in the branch name to dashes – e.g. if your repo name is `tangram-docs` and your branch name is `meetar/cleanup`, the reference in the `curl` command will look like `tangram-docs-meetar-cleanup`, and the full command will be:
    
    `curl -sL $(TANGRAM) | tar -zxv -C src-tangram --strip-components=2 tangram-docs-meetar-cleanup/pages`