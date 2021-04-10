<template>
  <div>
    <div class="home">
      <h2>VTK.js Vue Viewport Component</h2>
      <h5>What is this?</h5>
      <p>This is a set of re-usable components for displaying data with VTK.js</p>
      <h5>Why does it exist?</h5>
      <p>
        To provide a simple starting point for developers that wish to build Vue
        applications on top of VTK.js
      </p>
      <h3>Dicom Loader</h3>
      <examples-dicom-loader />

      <h3>Examples</h3>
      <div v-for="(example,i) in items" :key="i" class="mb-6">
        {{ example.title }} <br>
        {{ example.description }}
        <hr>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  data () {
    return {
      items: [
        {
          title: 'Basic Usage',
          url: 'basic',
          description:
            'How to use the component to render an array of vtkVolumes and manipulate their RGB Transfer Functions'
        },
        {
          title: 'Image Segmentation via Paint Widget',
          url: 'paintwidget',
          description:
            'This example demonstrates how to use VTK\'s PaintWidget and PaintFilter to perform manual segmentation.'
        },
        {
          title: 'MultiSlicePlanes',
          url: 'sliceplanes',
          description:
            'Demonstrates how to set up the Crosshairs interactor style and SVG Widget'
        },
        {
          title: 'Reslice Widget',
          url: 'reslice',
          description:
            'Implementing the ResliceWidget based on VTK examples but with a real volume'
        },
        {
          title: 'Full MPR Example',
          url: 'mpr',
          description: 'Example of a fully functional MPR implementation'
        },
        {
          title: 'MPR Manager',
          url: 'mpr-manager',
          description:
            'MPR manager component that hides internal logic but exposes needed events for modifying data'
        }
      ]
    }
  }
}
</script>

<style scoped>
* {color: #EDEDED; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";}
</style>
