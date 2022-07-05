# Renovate rewrites terraform lock constraints

With current repository renovate will rewrite `constraints` in
`.terraform.lock.hcl` file.

Current value `~> 4.0, >= 4.5.0`, Renovate rewrites to `~> 4.0`, expected value
should remain as is.

## Terraform behaviour

Terraform collects all provider requirements from current code and included
modules, combines them together and uses constraints field to calculate exact
provider version that complies give constraints.

Renovate rewriting this field has two problems:
- On terraform actions that change state, like changing module version or just
  doing init, terraform restores constraints field to expected value. This
  causes unnecessary diff between terraform and renovate.
- Losing vital information about negative constrains. Some module can define
  required version like `>= 4.5.0, < 5.0.0`, while main code requires just `>
  4.0.0`. Without this information from module constrains terraform eventually
  will pick incorrect version, leading to multitude of problems for end user.
