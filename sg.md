notFound - An optional boolean value to allow the page to return a 404 status and page.


redirect - An optional redirect value to allow redirecting to internal and external resources. 
It should match the shape of { destination: string, permanent: boolean }


If you want to get inferred typings for your props, you can use InferGetStaticPropsType<typeof getStaticProps>







Incremental Static Regeneration (ISR) enables you to use static-generation on a per-page basis, without needing to rebuild the entire site. 
With ISR, you can retain the benefits of static while scaling to millions of pages.



function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  )
}

// This function gets called at build time on server-side.
// It may be called again, on a serverless function, if
// revalidation is enabled and a new request comes in
export async function getStaticProps() {
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  return {
    props: {
      posts,
    },
    // Next.js will attempt to re-generate the page:
    // - When a request comes in
    // - At most once every 10 seconds
    revalidate: 10, // In seconds
  }
}

// This function gets called at build time on server-side.
// It may be called again, on a serverless function, if
// the path has not been generated.
export async function getStaticPaths() {
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // Get the paths we want to pre-render based on posts
  const paths = posts.map((post) => ({
    params: { id: post.id },
  }))

  // We'll pre-render only these paths at build time.
  // { fallback: blocking } will server-render pages
  // on-demand if the path doesn't exist.
  return { paths, fallback: 'blocking' }
}

export default Blog


 - Any requests to the page after the initial request and before 10 seconds are also cached and instantaneous.
 - Once the page has been successfully generated, Next.js will invalidate the cache and show the updated product page. If the background regeneration fails, the old page will stay unaltered.






GetStaticPaths (Static Generation) : 

If you export an async function called getStaticPaths from a page that uses dynamic routes, Next.js will statically pre-render all the paths specified by getStaticPaths.

export async function getStaticPaths() {
  return {
    paths: [
      { params: { ... } } // See the "paths" section below
    ],
    fallback: true, false, or 'blocking' // See the "fallback" section below
  };
}


When you use getStaticProps on a page with dynamic route parameters, you must use getStaticPaths.

You cannot use getStaticPaths with getServerSideProps.



