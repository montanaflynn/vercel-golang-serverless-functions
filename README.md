# Vercel Golang Serverless Functions

### Routing

Let's suppose we have an API with two routes:

```
/users
/users/:id
```

For vercel to make the routes we will the following directory structure:

```
/api/users.go
/api/users/[id].go
```

Since we don't want to have an `/api/` we can add these rules to `vercel.json`:

```json
{
  "framework": null,
  "rewrites": [
    { "source": "/:path*", "destination": "/api/:path*" }
  ],
  "redirects": [
    { "source": "/api/:path*", "destination": "/:path*", "permanent": true }
  ]
}
```

### Development & Deployment

Since vercel expects non-standard go file naming tools like `go vet` will complain and you'll get errors and your editor such as vscode won't be able to find the types or do autocomplete. To work around this we can put the source files in a folder called `pkg` and then during deployment copy the code to the `api` directory with the names vercel wants.

Check out [./deploy.sh](./deploy.sh) for an example of how this can work.

The only thing you need to do is make sure there's a function in every directory with a signature matching `func Handler(w http.ResponseWriter, r *http.Request)`. Check out the example files that return the path and query parameters:

- [./pkg/users.go](./pkg/users.go)
- [./pkg/users/users.go](./pkg/users/users.go)

Deploying is as simple as [installing the vercel cli](https://vercel.com/docs/cli) and running `vercel login` once, then using `vercel` or `vercel --prod` and following the instructions once. Subsequent deployments will be a single command and give you a deployment URL.
