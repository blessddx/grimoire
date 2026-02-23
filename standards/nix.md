# Nix for Dev Environments

> **Mode:** Inline | **When:** Always

All projects use Nix for reproducible development environments. Do not install tools globally or via Homebrew for project dependencies — use a `flake.nix` devShell.

---

## Rule

- **ALWAYS use Nix flakes** for dev environment setup
- **ALWAYS enter the dev shell** before running project commands (`nix develop` or direnv)
- **NEVER install project dependencies globally** — they belong in the flake
- **If a flake.nix exists**, read it to understand available tools before installing anything

---

## Starter flake.nix

When a project needs a Nix environment, start from this template:

```nix
{
  description = "Dev environment";

  inputs = {
    nixpkgs.url = "github:NixOS/nixpkgs/nixpkgs-unstable";
    flake-utils.url = "github:numtide/flake-utils";
  };

  outputs = { self, nixpkgs, flake-utils }:
    flake-utils.lib.eachDefaultSystem (system:
      let
        pkgs = nixpkgs.legacyPackages.${system};
      in
      {
        devShells.default = pkgs.mkShell {
          buildInputs = with pkgs; [
            # Add project tools here
          ];

          shellHook = ''
            echo "dev shell ready"
          '';
        };
      });
}
```

### Usage

```bash
# Enter the dev shell
nix develop

# Or with direnv (auto-enters on cd)
echo "use flake" > .envrc
direnv allow
```

### Adding tools

Add packages to `buildInputs`:

```nix
buildInputs = with pkgs; [
  nodejs_22
  pnpm
  ast-grep
  jj
];
```

Search for packages at [search.nixos.org](https://search.nixos.org/packages).
