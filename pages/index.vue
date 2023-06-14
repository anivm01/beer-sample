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
        position: relative;
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
    .dry{
        width: 150px;
        height: 150px;
        overflow: hidden;
        position: absolute;
        top: -10px;
        right: -10px;
    }
    
    .dry::before,.dry::after {
        position: absolute;
        z-index: -1;
        content: '';
        display: block;
        border: 5px solid #572677;
        border-top-color: transparent;
        border-right-color: transparent;
    }

    .dry::before {
        top: 0;
        left: 0;
    }
    .dry::after {
        bottom: 0;
        right: 0;
    }
    .dry_text {
        position: absolute;
        display: block;
        width: 225px;
        padding: 15px 0;
        background-color: #833ab4;
        box-shadow: 0 5px 10px rgba(0,0,0,.1);
        color: #fff;
        font: 700 18px/1 'Lato', sans-serif;
        text-shadow: 0 1px 1px rgba(0,0,0,.2);
        text-transform: uppercase;
        text-align: center;
        left: -25px;
        top: 30px;
        transform: rotate(45deg);
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
                    <div class="dry" v-if="beer.isDryHopped"><span class="dry_text">Dry Hopped</span></div>
                    <h2 class="name">{{ beer.name }}</h2>
                    <h3 class="tagline">{{ beer.tagline }}</h3>
                    <div><span class="label">ABV:</span><span>{{ beer.abv }}</span></div>
                    <div><span class="label">IBU:</span><span>{{ beer.ibu }}</span></div>
                    <p class="description">{{ beer.description }}</p>
                    <h4 v-if="beer.hasLactose">*This item contains Lactose</h4>
                </div>
            </li>
        </ul>
    </div>
</template>

