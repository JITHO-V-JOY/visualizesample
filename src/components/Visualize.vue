<template>
  <div class="visualizeContainer"></div>
</template>

<script>

let containerId = 0;

const jrsConfig = {
  name: "jasperadmin",
  password: "jasperadmin",
  instancePath: 'http://localhost:8082/jasperserver-pro'
}

let vizLoadingPromise = null;
const loadVisualize = () => {
  if (vizLoadingPromise) {
    // don't load visualize again if the loading has been started already
    // or if it has been loaded already
    return;
  }

  vizLoadingPromise = new Promise((resolve, reject) => {

    const onVisualizeLoadingError = (error) => {
      console.error('Got an error while loading visualize.js library! Please check if your JRS instance is up and running.');
      reject(error);
    };

    const visualizePath = jrsConfig.instancePath + '/client/visualize.js?_opt=false&userTimezone=India&userLocale=en';

    const body = document.getElementsByTagName("body")[0];
    const script = document.createElement("script");
    script.async = true;
    script.setAttribute("defer", "defer");
    script.src = visualizePath;
    script.onload = () => {
      resolve();
    };
    script.onerror = onVisualizeLoadingError;
    body.appendChild(script);
  });
};

const renderResource = (props) => {

  const mapTypeToMethod = {
    adhoc: 'adhocView',
    report: 'report',
    dashboard: 'dashboard',
  };

  let renderMethod = mapTypeToMethod[props.resourceType];
  if (!renderMethod) {
    console.log('Unknown resource type:', props.resourceType);
    return;
  }

  const onInitCallback = (visualizeInstance) => {
    visualizeInstance[renderMethod]({
      container: props.container,
      resource: props.resourcePath,
      success: function () {
        console.log('Resource has been rendered. The success() callback arguments are:');
        console.log(arguments);
      },
      error: function(error) {
        console.log('Resource has NOT been rendered. The error() callback arguments are:');
        console.log(arguments);
        console.log('The error is:', error);
      }
    });
  };

  const authConfig = {
    auth: {
      name: jrsConfig.name,
      password: jrsConfig.password
    }
  };

  window.visualize(authConfig, onInitCallback);
}

export default {
  name: 'Visualize',

  created: loadVisualize,

  mounted: function () {
    vizLoadingPromise.then(() => {

      // this is a temporary hack since visualize don't support
      // container in a form of DOM node or jQuery object, only selector
      const containerName = 'container' + containerId;
      this.$el.setAttribute('name', containerName);
      containerId++;

      renderResource({
        container: `[name=${containerName}]`,
        resourceType: this.resourceType,
        resourcePath: this.resourcePath
      });
    },() => {});
  },

  props: {
    resourceType: String,
    resourcePath: String
  }
}
</script>

<style scoped>
.visualizeContainer {
  width: 100%;
  height: 100%;
}
</style>
