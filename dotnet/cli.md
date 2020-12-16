# Dotnet Cli - comandos úteis

## Geral

Para listar os SKDs instalados

```shell
dotnet --list-sdks
```

Para obter informações gerais sobre o ambiente

```shell
dotnet --info
```

Para saber a versão do dotnet instalado

```shell
dotnet --version
```

Para mudar a versão padrão do SDK para uma que não é a padrão.
(*Verificar as versões instaladas com o comando acima*)

```shell
dotnet new globaljson --sdk-version 3.1.404
```

## Ferramentas

Para listar as ferramentas instaladas (global)

```shell
dotnet tool list -g
```

Para listar as ferramentas disponíveis (NuGet)

```shell
dotnet tool search entity
```
