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

<style scoped>
    .title{
        text-align: center;
        background: -webkit-linear-gradient(90deg, rgba(131,58,180,1) 0%, rgba(253,29,29,1) 50%, rgba(252,176,69,1) 100%);
        background-clip: text;
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;

    }
    .name {
        text-align: center;
        width: 100%;
        margin: 0;
    }
    .tagline {
        text-align: center;
        width: 100%;
        margin: 0;
    }
    .list {
        list-style: none;
        padding: 1rem;
        display: flex;
        flex-wrap: wrap;
        justify-content: center;
        gap: 2rem;
        max-width: 80rem;
        margin: 0 auto;
        color: white;
    }
    .item {
        display: flex;
        align-items: stretch;
        gap: 1rem;
        padding: 1rem;
        width: 100%;
        background: linear-gradient(90deg, rgba(131,58,180,1) 0%, rgba(253,29,29,1) 50%, rgba(252,176,69,1) 100%);
        border: 4px solid black;
    }
    .image {
        object-fit: contain;
        aspect-ratio: 1/1;
        width: 100%;
        height: 100%;
    }
    .image_box {
        width: 40%;
    }
    .details {
        padding: 0 2rem;
        width: 100%;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        gap: 1rem;
    }
    .label {
        margin-right: 1rem;
    }
    .description {
        font-weight: 100;
    }

    @media (max-width: 767px) {
        .item {
            flex-direction: column;
            align-items: center;
        }
    }
</style>

<template>
    <div>
        <h1 class="title">Brew Dog Beers</h1>
        <ul class="list">
            <li class="item" v-for="beer in beers" :key="beer.id">
                <div class="image_box">
                    <img class="image" :src=beer.image_url :alt="beer.name"/>
                </div>
                <div class="details">
                    <h2 class="name">{{ beer.name }}</h2>
                    <h3 class="tagline">{{ beer.tagline }}</h3>
                    <div><span class="label">ABV:</span><span>{{ beer.abv }}</span></div>
                    <div><span class="label">IBU:</span><span>{{ beer.ibu }}</span></div>
                    <h4 v-if="beer.hasLactose">This item contains Lactose</h4>
                    <p class="description">{{ beer.description }}</p>
                </div>
            </li>
        </ul>
    </div>
</template>

