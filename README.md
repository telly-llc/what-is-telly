<div>
  <img alt="Telly logo" src="https://avatars.githubusercontent.com/u/111651524?s=200&v=4"><br>
</div>

# Telly

### simple, privacy-friendly usage statistics for your open source software

## What is Telly and how can I (not) use it?

Telly is a telemetry platform designed to make it easy for open source package authors to improve their software while protecting their users' privacy.

If you develop software, you can use Telly to start collecting daily active user counts and feature usage statistics with just one line of code.  Our dashboard and hosted infrastructure are designed with principles like Privacy-by-Design and differential privacy to help you meet compliance requirements (like GDPR or the EU Privacy Directive).  

If you use software, you might have noticed a telemetry disclaimer or activity from Telly.  This page can help you understand how Telly works so you can decide whether you or your organization is comfortable with its usage.  If you decide you want to opt out of Telly for a specific package or disable it completely, instructions are provided below.

## I'm just a software user. What does this mean?
**We have another information page designed specifically for end-users - [USERS.md](USERS.md).**  

You might prefer reading that one instead of the rest of this.

## Why is Telly useful?
Building open source software is hard. Whether you're a commercial open source company or just sharing a hobby project, it can be nearly impossible to answer questions like:

 * How many people are actually using my software on a daily basis?
 * Which features or use cases are most popular?
 * Which versions of my software are still actively used?
 * Is anyone using my package on Ubuntu 12.04, Windows 7, or a Raspberry Pi?
 * Which versions or implementations of Python are my users using?
 * Is my software being installed or used from a private repository outside of PyPI?

Telly is designed to make answering these questions possible with minimal effort and without compromising user privacy.

## Why did we build Telly?
We've been working on open source projects for more than two decades, including prior commercial open source businesses. We're also active users of open source software and regularly encounter telemetry in frameworks like Next.js, Hugging Face, or .NET Core for Linux.

Telly is the tool we wish we'd had as software developers, built in a way that we'd actually accept as software users.

## How do I use Telly in my package?

Integrating Telly into your project is simple.  We provide clients for both Python and Node, with other languages on the way.  The easiest way to get started is to visit our site at [http://gotelly.io](https://gotelly.io), but you can also configure the client to log to your own collection endpoint.  

(Please note that if you collect the data yourself, achieving compliance with privacy regulations may be more difficult.)

## What data does Telly collect?

Telly can be configured to collect more or less information. Currently, this information is separated into two groups:

### Level I

* Python Interpreter (implementation, version, environment)
* Execution Platform (CPU, RAM, OS)
* Python Package Environment (packages, versions)
* Aggregate counts of method calls or class invocations (**no argument values ever**)

### Level II

By default, Level II information is always salted and hashed.  Values are never directly visible to any humans, and
Telly only stores values temporarily during each rolling analytics window. This approach, combined with differential
privacy techniques for statistics, allows us to estimate quantities the number of unique users and user behavior while 
reducing the likelihood of disclosing personally identifiable information.

Level II information currently includes:
* Salted SHA-256 of username
* Salted SHA-256 of user home path
* Salted SHA-256 of machine ID
* Salted SHA-256 of machine hostname

Two salting methods are currently available and can be set with the `TELLY_SALT_TYPE` environment variable:
 * `random_uuid4`: RFC 4122 UUID4 (`uuid.uuid4()`) **default** 
 * `machine_id`: Machine ID (`uuid.getnode()`)

The `random_uuid4` salt is calculated once and stored in `~/.telly_salt`.  If that path is not writable, such as in 
highly-restricted execution environments, Telly falls back on machine ID.  The quality of `uuid.uuid4()` varies 
across different implementations and versions of Python but is considered to be cryptographically secure
for salting purposes.

Note: For internal corporate usage of Telly, Level II information can be logged and made visible in the dashboard 
subject to contract terms.

## Supported languages and geographies

* Telly doesn't just support Python libraries. It's also compatible with Node via `Telly.js` and support for other
  languages is on the way.
* Telly lets you choose where data about your users is stored. We currently support fully-isolated environments in the
  US and EU.

## Sample notice language

In most circumstances, when you embed Telly in your project, you'll want to add a notice to your README.md that lets
your users know what information Telly is collecting and how you're using it to manage your project.

Here are a few examples of notices that you might start from:

**Example 1**

```
We want to make our software more useful and reliable.  In order to do that, we're using Telly, a privacy-friendly
usage statistics and support tool.  Telly may collect basic information about your Python and OS environment, allowing 
us to understand things like which operating systems and Python versions to support.   In some cases, Telly may also
collect SHA-256-hashed representations of information that allows us to estimate our unique number of users.  These 
hashes are only used temporarily to assign a temporary ID for aggregate reporting; no plaintext information is ever
transmitted or visible to humans and even hashes are only stored during a temporary, rolling analytics window.  

You can learn more about Telly's privacy functionality here: https://gotelly.io/.  

To disable Telly's data collection, you can either:
 * set the TELLY_DISABLE environment variable to a truthy value like Y or 1
 * create a file named .telly_disable in your home directory
```

**Example 2**

```
This software may collect information about you and your use of the software, which is shared with our telemetry 
partner, Telly. You can learn more about how we use this information at our website here: [your privacy notice]
```

## Verifying Telly distributions with sigstore
We use sigstore to sign Telly distributions.  You can use sigstore to verify the distributions; for example, you can check our Python distribution using the commands below:
```shell
$ pip3 install sigstore
$ python -m sigstore verify telly.py 
```


### How does Telly work (and when doesn't it)?
For Python and Node, Telly works by introspecting your module, decorating classes and methods, incrementing a global counter, 
and then spawning an async process to send the data to Telly when configured.  Telly works in almost all situations 
where Python and Node execute, including on CLI/REPL, Jupyter notebook, web apps, script, or a module imported from another 
module.  For some patterns where atexit methods are not registered, like multiprocessing, manual decoration may be 
required to ensure that data is collected.

Telly can also be configured to report data on a periodic basis for long-running jobs or "permanently-running" services
like those that frequently occur in data science, machine learning, and web application environments.  These settings
can be configured by libraries or through the  `TELLY_TIMER`  and `TELLY_TIMER_INTERVAL_SEC` environment variables. 

Telly also generally works in situations where signal stuff happens, like when processes are killed or inside an 
IDE or debugger like pdb, VS Code, PyCharm, or WebStorm.  By default, Telly will attempt to log the exit signal sent and return
signal handling to any previous handlers.  If you want Telly to only report on normal atexit, you can set the 
`TELLY_EXIT_SIGNAL_ONLY` environment variable to a truthy value..


### I'm a user.  Can I see what Telly is logging?
Absolutely! Just set `TELLY_SHOW_ME` to a truthy value like `true` or `1` and Telly will print what it transmits like this:
```
$ TELLY_SHOW_ME=1 python3 foo.py
```

### How can I opt in or out of a single project's telemetry?

You can opt in or out of a single project's telemetry using the Telly CLI methods:
 * Opt in: `python3 -m telly optin [project-uuid]`
 * Opt out: `python3 -m telly optout [project-uuid]`

These commands edit the `.telly_optin` file in your home directory.  You can always edit it manually or 
list its contents like this: `$ cat ~/.telly_optin`

### How can I enable or disable Telly completely?

If you want to disable or enable Telly for all projects, you can use the commands below:
*  Enable: `python3 -m telly enable`
 * Disable: `python3 -m telly disable`

These commands create or removes the `.telly_disable` file in your home directory.  You can always
create or delete it manually.


### Want to learn more?
Want to learn more about using Telly? Do you have an idea for new features? Just want to hear our voice?

* Email us: [contact@gotelly.io](mailto:contact@gotelly.io)
* Join our [Slack](https://gotelly.io/)
* Follow us on [Twitter](https://twitter.com/usetelly)
