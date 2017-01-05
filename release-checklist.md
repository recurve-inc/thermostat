# Checklist for releasing new thermostat package versions

These steps assume that the package has been installed locally with

`pip install -e .`

and

`pip install -r dev-requirements.txt`

## All releases (major, minor, patch)

### Before and after merging branches into develop or master:

#### Tests

- [ ] Tests are passing for all python versions locally
- [ ] Tests are passing for all python versions on travis
- [ ] Test coverage has not decreased

Tests are run on travis autmatically when code is pushed. Tests results can
be seen or run by anyone with a github account that has access to the
repository at:
[https://travis-ci.org/openeemeter/thermostat](https://travis-ci.org/openeemeter/thermostat)

Tests are run locally with the following command

`tox`

#### Documentation

- [ ] New functions and classes that have appropriate doc strings
- [ ] Sphinx documentation finds new fuctions and classes
- [ ] Usage, parameters, and return values for new functions and classes are documented
- [ ] Documentation builds with no warnings locally
- [ ] Documentation builds with no warnings on readthedocs

Documentation is built using the free readthedocs service.

The readthedocs service requires an account. The account administrator should
set the following build settings:

*Settings*

- Name: thermostat
- Repository URL: https://github.com/openeemeter/thermostat
- Repository Type: git
- Description: Calculate connected thermostat temperature/run-time savings
- Documentation Type: Sphinx HTML
- Language: English
- Programming Language: Python
- Project homepage: http://github.com/openeemeter/thermostat
- Tags: (any)

*Advanced Settings*

- Install your project inside a virtualenv using setup.py install: checked.
- Requirements file: `docs/rtd_requirements.txt`
- Single version: unchecked
- Python configuration file: blank
- Default branch: blank
- Default version: latest
- Enable PDF Build: checked
- Enable EPUB build: checked
- Privacy Level: public
- Use system packages: checked
- Python interpreter: CPython2.x

*Other*
- Github should be configured to push notifications to Readthedocs.org at
  [https://github.com/openeemeter/thermostat/settings/installations](https://github.com/openeemeter/thermostat/settings/installations)

#### Code

- [ ] Should be reviewed by a team member
- [ ] Should conform to PEP 008 (use flake8)

#### Pull requests

- [ ] Should point at develop branch (not master)

### Release flow (optionally use the git-flow tool to help enforce this pattern)

#### 1. Merge feature branch(es) into `develop`
```
   cd thermostat
   git checkout develop
   git merge my-feature-branch develop
```

#### 2. Create release branch
* Verify the latest tagged release on github
* Determine the next version you'll be releasing using [semantic versioning](http://semver.org/)
* Create release branch from `develop`, named in the form _release/vMAJOR.MINOR.PATCH-TAG_, for example _release/v0.4.13-alpha_

  ```
    git checkout -b release/vMAJOR.MINOR.PATCH-TAG
  ```

* Update the version number in the following files:
  * `docs/conf.py`: the release and version strings
  * `thermostat/__init__.py`: the `VERSION` tuple
  * `docs/tutorial.rst`: the output string from the line `import thermostat; thermostat.get_version()`
* Publish release branch
  ```
    git push origin release/vMAJOR.MINOR.PATCH-TAG
  ```

#### 3. Tag the release
* Merge release branch into `master`

  ```
    git checkout master
    git merge release/vMAJOR.MINOR.PATCH-TAG master
  ```

* Tag the release with its name on `master`

  ```
    git checkout master   # ensure you're on the right branch
    git tag vMAJOR.MINOR.PATCH-TAG
    git push origin --tags
    git push origin master
  ```

* Merge master into develop

  ```
    git checkout develop
    git merge master
    git push origin develop
    git branch -d release/vMAJOR.MINOR.PATCH-TAG  # Remove the local release branch
  ```

#### 4. Prepare distributions
* Build a source distribution

  ```
    python setup.py sdist
  ```
* Build a universal wheel distribution

  ```
    python setup.py bdist_wheel --universal
  ```
* Publish both distributions to the Python Pacakge Index (PyPI)

  ```
    twine upload dist/<dist-filename>
  ```
* Verify thermostat package version
  * Install thermostat by running `pip install thermostat --upgrade --no-deps`
  * Start python shell and run the following:

  ```
    >>> import thermostat
    >>> print(thermostat.get_version())
    1.0.0
  ```


## Extra steps for patch releases

None

## Extra steps for minor releases

None

## Extra steps for major releases

None
