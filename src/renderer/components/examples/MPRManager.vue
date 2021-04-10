<template>
  <div>
    <h2>MPR Manager</h2>
    <p>Integrating the MPR manager that acts as a single component, allowing for a state manager to provide data.</p>
    <select v-model="selectedFile">
      <option v-for="file in files" :key="file">{{ file }}</option>
    </select>
    <hr>
    <div v-if="loading">
      <h3>Loading...</h3>
    </div>
    <div v-else>
      <div>
        <button :class="{ active: activeTool === LEVEL_TOOL }" @click="selectTool('LEVEL')">
          <img src="https://img.icons8.com/material-rounded/344/contrast.png">
          Level
        </button>
        <button :class="{ active: activeTool === SELECT_TOOL }" @click="selectTool('SELECT')">
          <img src="https://img.icons8.com/material/344/define-location.png">
          Select
        </button>
        <span>
          <input id="checkbox" v-model="syncWindowLevels" type="checkbox">
          <label for="checkbox">Sync Window Leveling</label>
        </span>
        <table>
          <tr>
            <td>
              Blend Mode for active Window:
              <b>{{ activeWindowTitle }}</b>
            </td>
          </tr>
          <tr>
            <td>
              <input id="1blend" v-model="activeView.blendMode" type="radio" value="none">
              <label for="1blend">None</label>

              <input id="2blend" v-model="activeView.blendMode" type="radio" value="MIP">
              <label for="2blend">MIP</label>

              <input id="3blend" v-model="activeView.blendMode" type="radio" value="MINIP">
              <label for="3blend">MinIP</label>

              <input id="4blend" v-model="activeView.blendMode" type="radio" value="AVG">
              <label for="4blend">Average</label>
            </td>
          </tr>
        </table>
      </div>
      <MPRManager
        :volume-data="volumeData"
        :view-data="viewData"
        :sync-window-levels="syncWindowLevels"
        :active-tool="activeTool"
        @rotate="onRotate"
        @thickness="onThickness"
        @activity="onActivity"
        @windowLevels="onWindowLevels"
      />
    </div>
  </div>
</template>

<script>
import {
  MPRManager,
  View2dMPR,
  vtkInteractorStyleMPRCrosshairs,
  vtkInteractorStyleMPRWindowLevel,
  vtkSVGWidgetManager,
  vtkSVGCrosshairsWidget
} from '@/library'

import vtkHttpDataSetReader from 'vtk.js/Sources/IO/Core/HttpDataSetReader'

import { files } from '@/components/examples'
import { LEVEL_TOOL, SELECT_TOOL } from '@/library/VTKViewport/consts'

export default {
  components: {
    MPRManager
  },
  data () {
    return {
      LEVEL_TOOL,
      SELECT_TOOL,
      volumeData: null,
      activeTool: LEVEL_TOOL,
      loading: true,
      selectedFile: files[2],
      syncWindowLevels: false,
      viewData: {
        top: {
          color: '#F8B42C',
          slicePlaneNormal: [0, 0, 1],
          sliceViewUp: [0, -1, 0],
          slicePlaneXRotation: 0,
          slicePlaneYRotation: 0,
          viewRotation: 0,
          sliceThickness: 0.1,
          blendMode: 'none',
          window: {
            width: 0,
            center: 0
          },
          active: true
        },
        left: {
          color: '#A62CF8',
          slicePlaneNormal: [1, 0, 0],
          sliceViewUp: [0, 0, -1],
          slicePlaneXRotation: 0,
          slicePlaneYRotation: 0,
          viewRotation: 0,
          sliceThickness: 0.1,
          blendMode: 'none',
          window: {
            width: 0,
            center: 0
          },
          active: false
        },
        front: {
          color: '#2C92F8',
          slicePlaneNormal: [0, -1, 0],
          sliceViewUp: [0, 0, -1],
          slicePlaneXRotation: 0,
          slicePlaneYRotation: 0,
          viewRotation: 0,
          sliceThickness: 0.1,
          blendMode: 'none',
          window: {
            width: 0,
            center: 0
          },
          active: false
        }
      }
    }
  },
  computed: {
    activeView () {
      return Object.values(this.viewData).find(v => v.active)
    },
    activeWindowTitle () {
      const res = Object.entries(this.viewData).find(([i, v]) => v.active)
      return res[0]
    }
  },
  watch: {
    selectedFile (newVal) {
      this.loadData(newVal)
    }
  },
  created () {
    // non-reactive data
    this.files = files
  },
  mounted () {
    this.loadData()
  },
  beforeDestroy () {},
  methods: {
    selectTool (tool) {
      this.activeTool = tool
    },

    onRotate ({ index, plane, angle }) {
      this.viewData[index][`slicePlane${plane.toUpperCase()}Rotation`] = angle
    },
    onThickness ({ index, thickness }) {
      this.viewData[index].sliceThickness = thickness
      // TODO: change MIP style
    },
    onActivity (index) {
      Object.entries(this.viewData).forEach(([i, v]) => {
        v.active = i === index
      })
    },
    onWindowLevels ({ index, windowCenter, windowWidth }) {
      // Only set the view data that is displayed. The actual values are handled internally?
      // TODO: Confirm that this is ok, maybe the children should listen to changes to these values and update on their own?
      this.viewData[index].window.center = windowCenter
      this.viewData[index].window.width = windowWidth
    },
    loadData (fileString) {
      this.loading = true
      const reader = vtkHttpDataSetReader.newInstance({
        fetchGzip: true
      })

      reader
        .setUrl(`/${fileString || this.selectedFile}`, { loadData: true })
        .then(() => {
          this.volumeData = reader.getOutputData()
          this.loading = false
        })
    }
  }
}

</script>

<style scoped>
button {
  font-size: 14px;
  line-height: 20px;
}
button > img {
  vertical-align: middle;
  height: 20px;
}
button + button {
  margin-left: 8px;
}

button.active {
  background: #77b0df;
  border-color: #1370bd;
}

label + input {
  margin-left: 12px;
}
</style>
