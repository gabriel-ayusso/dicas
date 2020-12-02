# Health Check com .Net Core
Health Check é uma forma de informar aos serviços de monitoramento (como o Prometheus, por exemplo) ou orquestradores (como o Kubernetes) sobre a saúde da sua API. 

Em geral, há dois tipos de checagens, a saber, o **health check**, e o **readiness**. 

* O **healthcheck** verifica se a API está saudável e respondendendo às requisições. Se o serviço estiver em pé, ele vai responder.

* O **readiness**, por sua vez, já verifica as dependências da API, como o banco de dados, por exemplo. Normalmente esse método faz uma query simples (exemplo ```SELECT 1```) apenas para verificar se o BD está no ar. Importante que seja algo simples o suficiente para não sobrecarregar a base de dados, o que pode interferir no desempenho da aplicação.

## Referências
[Referência - Docs Microsoft](https://docs.microsoft.com/en-us/dotnet/architecture/microservices/implement-resilient-applications/monitor-app-health)

Primeiramente, vamos instalar o pacote necessário:
```shell
 Microsoft.Extensions.Diagnostics.HealthChecks
```

O pacote de Health Check da Microsoft já possui inúmeros *health checks* implementados para nós, no entanto, podemos implementar o nosso próprio health check, se for conveniente.

Para saber mais ou ver os health checks disponíveis, dê uma olhada em https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks#health-checks

## Hands On
Vamos implementar um health check customizado. Para fins de estudo, nosso nosso health check retornará "Healty", indicando que o serviço está de pé. As respostas possíveis são: 
* **Degraded** (Degradado, ou seja, o serviço está funcionando, mas com lentidão)
* **Healty** (Saudável: O serviço está operando normalmente)
* **Unhealty** (Não saudável: O serviço apresenta problemas)

Antes de mais nada, precisamos importar as dependências:
```csharp
using Microsoft.Extensions.Diagnostics.HealthChecks;
```

Para isso, vamos implementar uma classe dentro do arquivo **Startup.cs**, após o fim da classe Startup (mas dentro do namespace)
```csharp
public class MyCustomHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, CancellationToken cancellationToken = default)
    {
        // checa se a aplicação, dependências, banco de dados estão ok, se sim, retorna
        return Task.FromResult(HealthCheckResult.Healthy());
    }
}
```

Uma vez que nossa classe está criada, vamos adicionar e configurar o middleware.

## Default Health Check
Para usar o Healh Check padrão, vamos adicionar no nosso método **Configure**:

```csharp
app.UseEndpoints(endpoints =>
{
    // ...
    //GAG - Incluído o método MapHealthChecks
    endpoints.MapHealthChecks("/hc", new HealthCheckOptions
    {
        Predicate = _ => true,
        ResponseWriter = UIResponseWriter.WriteHealthCheckUIResponse
    });
    // ...
});
```

Pronto! Para testar, basta acessar a url http://localhost:5000/hc

## Custom Health Check

Primeiro, vamos configurar o middleware, no método **ConfigureServices**:
```csharp
services.AddHealthChecks().AddCheck("custom-check", new MyCustomHealthCheck());
```

Depois, no método **Configure**, adicionar os mapeamentos ao endpoint:
```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    //GAG: Adicionar essa linha
    endpoints.MapHealthChecks("/hc");
});
```

Com isso está pronta nossa aplicação. Podemos executá-la e verificar o health-check funcionando na url http://localhost:5000/hc.

## Prometheus Exporter
Para adicionar o exporter que pode ser consumido pelo Prometheus, primeiro vamos adicionar o pacote a seguir

```shell
dotnet add package AspNetcore.HealthChecks.Publisher.Prometheus
dotnet add package AspNetCore.HealthChecks.Prometheus.Metrics
```

No método Configure, vamos adicionar a extensão que inclui o endpoint com as métricas:

```csharp
app.UseHealthChecksPrometheusExporter("/metrics");
```

Para acessar as métricas do Prometheus, navegar no link http://localhost:5000/metrics

## Health Check UI
O módulo health check também oferece uma interface de usuário onde podemos visualizar a saúde dos nossos serviços. Para implementá-la, primeiro vamos adicionar os pacotes necessários:

```shell
dotnet add package AspNetCore.HealthChecks.UI
dotnet add package AspNetCore.HealthChecks.UI.InMemory.Storage
```

Adicionar as referências:
```csharp
using HealthChecks.UI.Core;
using HealthChecks.UI.InMemory.Storage;
```

No método **ConfigureServices**:

```csharp
services.AddHealthChecksUI().AddInMemoryStorage();
```

No método **Configure**:
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseEndpoints(config =>
    {
        config.MapHealthChecksUI();
    });
}
```

### Configuração 
No arquivo appsettings.json, incluir as linhas:
```json
{
    "HealthChecksUI": {
        "HealthChecks": [
            {
                "Name": "HTTP-Api-Basic",
                "Uri": "http://localhost:5000/healthz"
            }
        ],
        "Webhooks": [
            {
                "Name": "",
                "Uri": "",
                "Payload": "",
                "RestoredPayload": ""
            }
        ],
        "EvaluationTimeInSeconds": 10,
        "MinimumSecondsBetweenFailureNotifications": 60
    }
}
```


Para acessar a interface, utilizar a url http://localhost:5000/healthchecks-ui