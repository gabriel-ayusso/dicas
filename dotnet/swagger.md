# .Net API com Swagger
Para adicionar o swagger em sua api, será necessário instalar o pacote [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore)

## Instalando o pacote
```bash
dotnet add package Swashbuckle.AspNetCore
```

## Adicionando o middleware Swagger

No topo do arquivo **Startup.cs**, inclua as dependências
```c#
using Microsoft.OpenApi.Models;

```

No arquivo Startup.cs, adicione o seguinte comando no método **ConfigureServices**:
```c#
// Adicionando o serviço Swagger
services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo { Title = "health_check_test_api", Version = "v1" });
});
```

No método **Configure**, altere o trecho a seguir:
```c#
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    // Adicionar as duas linhas abaixo
    app.UseSwagger();
    app.UseSwaggerUI(c => c.SwaggerEndpoint("/swagger/v1/swagger.json", "health_check_test_api v1"));
}
```

Para acessar a documentação Swagger, navege pela url ```/swagger``` do seu serviço.