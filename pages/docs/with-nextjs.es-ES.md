import Callout from 'nextra-theme-docs/callout'

# Uso con Next.js

## Obtención de datos del lado del cliente

Si su página contiene datos que son actualizados frecuentemente y no necesita renderizar previamente los datos, SWR se adapta perfectamente y no se necesita una configuración especial: solo importe `useSWR` y use el hook dentro de cualquier componente que use los datos.

Así es como funciona:

- En primer lugar, muestre inmediatamente la página sin datos. Puede mostrar un loading state para los datos que faltan.

- A continuación, se obtienen los datos en el lado del cliente y se muestran cuando están listos.

Este enfoque funciona bien, por ejemplo, para páginas que son dashboard. Dado que un dashboard es una página privada y específica del usuario, el SEO no es relevante y la página no necesita ser pre-renderizada. Los datos se actualizan con frecuencia, lo que requiere la obtención de datos en el momento de la solicitud.

## Pre-renderizado con datos por defecto

Si la página debe ser pre-renderizada, Next.js soporta [2 formas de pre-renderizado](https://nextjs.org/docs/basic-features/data-fetching):   
**Static Generation (SSG)** y **Server-side Rendering (SSR)**.

Junto con SWR, usted puede pre-renderizar la página para SEO, y también tener funcionalidades como caché, revalidación, *focus tracking*, o *refetching* en el lado del cliente.

Puede usar la opción `fallback` de [`SWRConfig`](/docs/global-configuration) para pasar los datos obtenidos previamente como el valor inicial de todos los hooks de SWR.
Por ejemplo con `getStaticProps`:

```jsx
 export async function getStaticProps () {
  // `getStaticProps` se ejecuta en el lado del servidor
  const article = await getArticleFromAPI()
  return {
    props: {
      fallback: {
        '/api/article': article
      }
    }
  }
}

function Article() {
  // `data` siempre estará disponible como es en `fallback`
  const { data } = useSWR('/api/article', fetcher)
  return <h1>{data.title}</h1>
}

export default function Page({ fallback }) {
  // Los hooks de SWR dentro del límite de `SWRConfigp siempre usarán esos valores
  return (
    <SWRConfig value={{ fallback }}>
      <Article />
    </SWRConfig>
  )
}
```

La página sigue estando pre-renderizada. Es buena para SEO, rápida en la respuesta, pero también totalmente impulsada por SWR en el lado del cliente. Los datos pueden ser dinámicos y auto-actualizados a lo largo del tiempo.

<Callout emoji="💡">
  El componente `Article` renderizará los datos pre-generados primero, y después de que la página se hidrate, obtendrá los últimos datos de nuevo para mantenerla actualizada.
</Callout>
