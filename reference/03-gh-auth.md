# 🔐 Configuring GitHub authentication

GitHub authentication on shared cloud infrastructure is complicated.

Always remember, "cloud" is the same as "someone else's computer".
It's not a good idea to store your credentials on someone else's computer!

For this reason, we use a special tool called `gh-scoped-creds` to provide a special
credential which grants restricted access to only certain GitHub resources for a limited
amount of time.

For more info, see the
[full docs for using `gh-scoped-creds` on 2i2c JupyterHubs](https://2i2c.org/community-showcase/user/topics/data/git.html#using-gh-scoped-creds).

## Run `gh-scoped-creds`

At the terminal, run the command:

```bash
gh-scoped-creds
```

Follow the printed instructions carefully!
