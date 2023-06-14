<script setup>

const { data: beers } = await useFetch('https://api.punkapi.com/v2/beers?brewed_after=11-2012', { 
  transform: (beers) => {
    //filter out the items that are not needed. (Remove any with Centennial hops)
    const filteredBeers = beers.filter((beer) => {
      return !beer.ingredients.hops.some((hop) => {
        return hop.name === 'Centennial';
      });
    });

    //map over the original data set to take only the necessary information
    const mappedBeers = filteredBeers.map(beer => {
        //find the items that are dry hopped
      const isDryHopped = beer.ingredients.hops.some((hop) => {
        return hop.add === 'dry hop';
      });

        //find the items that contain lactose
      const hasLactose = beer.ingredients.hops.some((hop) => {
        return hop.name === 'Lactose';
      });

      return {
        name: beer.name,
        tagline: beer.tagline,
        description: beer.description,
        image_url: beer.image_url,
        abv: beer.abv,
        ibu: beer.ibu,
        isDryHopped: isDryHopped,
        hasLactose: hasLactose
      }
    })
        //sort the beers by ascending ABV
    const sortedBeers = mappedBeers.sort((a, b) => a.abv - b.abv)
    
    return sortedBeers
  }
})
</script>

<template>
    <div>
        <h1>Brew Dog Beers</h1>
        <div>
            <div v-for="beer in beers" :key="beer.id">
                <h2>{{ beer.name }}</h2>
                <h3>{{ beer.tagline }}</h3>
                <div><span>ABV:</span><span>{{ beer.abv }}</span></div>
                <div><span>IBU:</span><span>{{ beer.ibu }}</span></div>
                <h4 v-if="beer.hasLactose">This item contains Lactose</h4>
                <p>{{ beer.description }}</p>
                <img :src=beer.image_url :alt="beer.name"/>
            </div>
        </div>
    </div>
</template>