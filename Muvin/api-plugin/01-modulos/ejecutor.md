# Ejecutor — Orquestador de Procesos

**Ruta:** `source/components/Ejecutor.php`

## Propósito

`Ejecutor` es el corazón del plugin. Es la clase que orquesta la ejecución de todos los procesos activos para todos los clientes activos. Es invocado tanto por el comando de consola `yii lector` como por el endpoint HTTP `GET /plugin`.

## Método principal: `correrProcesos()`

```php
class Ejecutor {
    static public function correrProcesos() {
        $clientes = Cliente::find()->where(['activo' => 1])->all();
        
        $results = [];
        foreach ($clientes as $cliente) {
            $configuraciones = ClienteConfiguracion::find()
                ->where(['cliente_id' => $cliente->id, 'activo' => 1])
                ->all();
            
            foreach ($configuraciones as $client_conf) {
                $procesos = Proceso::find()
                    ->where(['cliente_id' => $cliente->id, 'activo' => 1])
                    ->all();
                
                foreach ($procesos as $proc) {
                    $resultado = call_user_func($proc->metodo, $client_conf);
                    $results[] = $resultado;
                }
            }
        }
        
        return $results;
    }
}
```

## Flujo de ejecución

```
correrProcesos()
  │
  ├─ Cliente::find() WHERE activo=1
  │    └─ [cliente1, cliente2, ...]
  │
  └─ foreach cliente:
       ├─ ClienteConfiguracion::find() WHERE cliente_id=X, activo=1
       │    └─ [conf1, conf2, ...]
       │
       └─ foreach configuracion:
            ├─ Proceso::find() WHERE cliente_id=X, activo=1
            │    └─ [proc1, proc2, ...]
            │
            └─ foreach proceso:
                 └─ call_user_func(proc->metodo, conf)
                      └─ ProcesoXxx::DescargarMails($conf)
```

## Ejemplo de datos en BD

| cliente.id | cliente.nombre | cliente_configuracion.srvmail | proceso.metodo |
|---|---|---|---|
| 1 | Empresa Ejemplo | mail.ejemplo.com | `app\components\Plugin\Cupos\ProcesoAca::DescargarMails` |
| 1 | Empresa Ejemplo | mail.ejemplo.com | `app\components\Plugin\Cupos\ProcesoBunge::DescargarMails` |
| 2 | Otra Empresa | mail.otra.com | `app\components\Plugin\Cupos\ProcesoCofco::DescargarMails` |

## Consideraciones

### ⚠️ Riesgo: call_user_func desde BD
El campo `proceso.metodo` se ejecuta directamente con `call_user_func()`. Si la base de datos es comprometida, un atacante podría insertar un callable malicioso. Ver [riesgos-seguridad.md](../08-riesgos-y-deuda-tecnica/riesgos-seguridad.md).

### ⚠️ Sin control de concurrencia
No hay mecanismo de lock. Si una ejecución del cron tarda más de 15 minutos, la siguiente se iniciará en paralelo, pudiendo procesar los mismos emails dos veces.

### ⚠️ Sin retry automático
Si `call_user_func` lanza una excepción, el proceso se interrumpe sin reintentar. La gestión de errores depende de cada `Proceso*.php`.
