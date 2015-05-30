# gopher

Go dependency management is ... an interesting problem.  The official
recommendation from Google is vendoring (via something like godeps),
but there are many people who hate that solution (or non-solution,
depending on who you ask).  Gopkg.in is often considered a much
cleaner alternative, but it often requires the library maintainers to
know about and support it.  Other alternatives tend toward yet another
form of vendoring.

So I suppose I felt like throwing another option into the mix.  I
don't think it's correct, but it might sometimes be necessary.

## ideas / goals

1. I want the syntax to be as familiar as possible to people used to
`go get`.
2. I want a project to be able to state its requirements in a single,
easy-to-access location, preferably within the project itself.
3. I want a simple method for freezing dependencies when a version
hits end-of-life *without* vendoring.
4. I don't want to rely on submodules.

## syntax

Right now, this is just a thought in my head, but just look at the
following commands, and imagine they worked as you see them working:

```
$ gopher gather github.com/nelsam/gopher -v

Reading dependencies and comparing against .deps.yaml

.........

github.com/go-gorp/gorp
 - github.com/go-gorp/gorp@a0770fda7b8546e4d4109820de86608a8682b447
github.com/nelsam/gorq
 - github.com/nelsam/gorq@a2b0a1694365bdc90ba1b39f34ae4649e6735aae

$ gopher update github.com/go-gorp/gorp
$ gopher use github.com/some-company/gorq as github.com/nelsam/gorq
$ gopher status

Checking dependencies...

github.com/go-gorp/gorp
 - remote: github.com/go-gorp/gorp
 - target: 9008e6d2b810022be6f673ff98d9ad1717f9b126

github.com/nelsam/gorq
 - remote: github.com/some-company/gorq
 - target: master

$ gopher freeze -v

Storing current remotes/commits in .deps.yaml
........
github.com/nelsam/gorq: current target is a branch, using current commit
 - github.com/some-company/gorq@5985166a9044b852a1cbb8c09fb4f3736f2f73c8

$ git add .deps.yaml
$ git push
```

Let's just say that you run `gopher gather some/go-gettable/project`
and it reads that .deps.yaml file in the project's root.  It then goes
about downloading all dependencies, just as `go get` would, and reads
.deps.yaml for each of them.  The .deps.yaml file simply stores a list
of dependencies that shouldn't be cloned as-is, and keeps track of the
remote and checkout target to be used when gathering them.
