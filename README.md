# Teamocil

Teamocil is a simple tool used to automatically create sessions, windows and splits in `tmux` with Ruby and YAML. Like [tmuxinator](https://github.com/aziz/tmuxinator), but with splits, not just windows.

## Usage

    $ gem install teamocil
    $ mkdir ~/.teamocil
    $ teamocil --edit sample
    $ tmux
    $ teamocil sample

## Options

* `--here` opens the session in the current window, it doesn’t create an empty first window.
* `--layout` takes a custom file path to a YAML layout file.
* `--edit` opens the layout file (whether or not `--layout` is used) with `$EDITOR`.

## Layout file structure

A layout file is a single YAML file located in `~/.teamocil` (eg. `~/.teamocil/my-project.yml`).

### Session

You can wrap your entire layout file in a `session` and Teamocil will rename the current session (so that you can find it more easily when running `tmux list-sessions`) before creating your windows.

#### Keys

* `name` (the name of the session)

#### Example

    session:
	  name: my-awesome-session
	  windows:
		[windows list]

### Windows

If you are not using a top-level `session` key, then the first key of your layout file will be `windows`, an array of window items.

#### Item keys

* `name` (the name that will appear in `tmux` statusbar)
* `root` (the directory in which every split will be created)
* `splits` (an array of split items)

#### Example

	windows:
      - name: my-first-window
        root: ~/Projects/foo-www
        splits:
          [splits list]
      - name: my-second-window
        root: ~/Projects/foo-api
        splits:
          [splits list]
      - name: my-third-window
        root: ~/Projects/foo-daemons
        splits:
          [splits list]

### Splits

Every window must define an array of splits that will be created within it. A vertical or horizontal split will be created, depending on whether the `width` or `height` parameter is used.

#### Item keys

* `cmd` (the commands to initially execute in the split)
* `width` (the split width, in percentage)
* `height` (the split width, in percentage)
* `target` (the split to set focus on, before creating the current one)

#### Example

	windows:
      - name: my-first-window
        root: ~/Projects/foo-www
        splits:
          - cmd: "git status"
          - cmd: "bundle exec rails server --port 4000"
            width: 50
          - cmd:
              - sudo service memcached start
              - sudo service mongodb start
            height: 50

## Layout examples

### Simple two splits window

#### Content of `~/.teamocil/sample-1.yml`

    windows:
      - name: sample-two-splits
        root: ~/Code/sample/www
        splits:
          - cmd:
            - pwd
            - ls -la
          - cmd: rails server --port 3000
            width: 50

#### Result of `$ teamocil sample-1`

    .------------------.------------------.
    | (0)              | (1)              |
    |                  |                  |
    |                  |                  |
    |                  |                  |
    |                  |                  |
    |                  |                  |
    |                  |                  |
    |                  |                  |
    |                  |                  |
    '------------------'------------------'

### Four tiled splits window

#### Content of `~/.teamocil/sample-2.yml`

    windows:
      - name: sample-four-splits
        root: ~/Code/sample/www
        splits:
          - cmd: pwd
          - cmd: pwd
            width: 50
          - cmd: pwd
            height: 50
            target: bottom-right
          - cmd: pwd
            height: 50
            target: bottom-left

#### Result of `$ teamocil sample-2`

    .------------------.------------------.
    | (0)              | (1)              |
    |                  |                  |
    |                  |                  |
    |                  |                  |
    |------------------|------------------|
    | (3)              | (2)              |
    |                  |                  |
    |                  |                  |
    |                  |                  |
    '------------------'------------------'

## Extras

### Zsh autocompletion

To get autocompletion when typing `teamocil <Tab>` in a zsh session, add this line to your `~/.zshrc` file:

    compctl -g '~/.teamocil/*(:t:r)' teamocil

## Contributors

Feel free to contribute and submit issues/pull requests [on GitHub](https://github.com/remiprev/teamocil/issues), just like these fine folks did:

* Samuel Garneau ([garno](https://github.com/garno))
* Jimmy Bourassa ([jbourassa](https://github.com/jbourassa))

## License

Teamocil is © 2011 Rémi Prévost and may be freely distributed under the [LITL license](http://litl.info/). See the `LICENSE` file.
