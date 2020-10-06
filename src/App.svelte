<script>
  import SortableList from "svelte-sortable-list";
  //   import { screenshot, download } from "image-screenshot";
  function screenshot(img, format = "png", quality = 0.97) {
    const canvas = document.createElement("canvas");

    let ratio = img.naturalWidth / img.naturalHeight;
    let width = img.height * ratio;
    let height = img.height;
    if (width > img.width) {
      width = img.width;
      height = img.width / ratio;
    }

    canvas.width = width;
    canvas.height = height;

    const context = canvas.getContext("2d");
    context.filter = getComputedStyle(img).filter;

    img.setAttribute("crossOrigin", "anonymous");

    context.drawImage(img, 0, 0, canvas.width, canvas.height);

    console.log(img, img.width, img.height);
    const url = canvas.toDataURL(`image/${format}`, quality);
    let image = new Image();
    image.src = url;
    let w = window.open("about:blank");
    setTimeout(function () {
      w.document.write(image.outerHTML);
    }, 0);

    return {
      url,
      then: (cb) => {
        cb(url);
      },
      download: () => {
        console.log(url);
        if (!window.open(url)) {
          document.location.href = url;
        }
      },
    };
  }
  let imgDOM;
  let list = [
    "blur",
    "brightness",
    "contrast",
    "dropShadow",
    "grayscale",
    "hue",
    "invert",
    "opacity",
    "saturate",
    "sepia",
  ];
  let blurInit = 0,
    brightnessInit = 1,
    contrastInit = 1,
    grayscaleInit = 0,
    hueInit = 0,
    invertInit = 0,
    opacityInit = 1,
    saturateInit = 1,
    sepiaInit = 0,
    dropXInit = 0,
    dropYInit = 0,
    dropBlurInit = 0,
    dropColorInit = "#000000";

  let blur = blurInit,
    brightness = brightnessInit,
    contrast = contrastInit,
    grayscale = grayscaleInit,
    hue = hueInit,
    invert = invertInit,
    opacity = opacityInit,
    saturate = saturateInit,
    sepia = sepiaInit,
    dropX = dropXInit,
    dropY = dropYInit,
    dropBlur = dropBlurInit,
    dropColor = dropColorInit;

  let blurString,
    brightnessString,
    contrastString,
    dropString,
    grayscaleString,
    hueString,
    invertString,
    opacityString,
    saturateString,
    sepiaString;

  //   let blurString = ` blur(${blur}px)`,
  //     brightnessString = `brightness(${brightness})`,
  //     contrastString = `contrast(${contrast})`,
  //     dropString = `drop-shadow(${dropX}px ${dropY}px ${dropBlur}px ${dropColor})`,
  //     grayscaleString = `grayscale(${grayscale})`,
  //     hueString = `hue-rotate(${hue}deg)`,
  //     invertString = `invert(${invert})`,
  //     opacityString = `opacity(${opacity})`,
  //     saturateString = `saturate(${saturate})`,
  //     sepiaString = `sepia(${sepia})`;

  function initVals() {
    (blur = blurInit),
      (brightness = brightnessInit),
      (contrast = contrastInit),
      (grayscale = grayscaleInit),
      (hue = hueInit),
      (invert = invertInit),
      (opacity = opacityInit),
      (saturate = saturateInit),
      (sepia = sepiaInit),
      (dropX = dropXInit),
      (dropY = dropYInit),
      (dropBlur = dropBlurInit),
      (dropColor = dropColorInit);
  }

  const initList = list;

  $: {
    //is the value different from defualt ones?
    (blurString = blur !== blurInit ? `blur(${blur}px)` : ""),
      (brightnessString =
        brightness !== brightnessInit ? `brightness(${brightness})` : ""),
      (contrastString =
        contrast !== contrastInit ? `contrast(${contrast})` : ""),
      (dropString =
        dropX !== dropXInit ||
        dropY !== dropYInit ||
        dropBlur !== dropBlurInit ||
        dropColor !== dropColorInit
          ? `drop-shadow(${dropX}px ${dropY}px ${dropBlur}px ${dropColor})`
          : ""),
      (grayscaleString =
        grayscale !== grayscaleInit ? `grayscale(${grayscale})` : ""),
      (hueString = hue !== hueInit ? `hue-rotate(${hue}deg)` : ""),
      (invertString = invert !== invertInit ? `invert(${invert})` : ""),
      (opacityString = opacity !== opacityInit ? `opacity(${opacity})` : ""),
      (saturateString =
        saturate !== saturateInit ? `saturate(${saturate})` : ""),
      (sepiaString = sepia !== sepiaInit ? `sepia(${sepia})` : "");

    //map list items to current values
    let filterStringMap = new Map([
      ["blur", blurString],
      ["brightness", brightnessString],
      ["contrast", contrastString],
      ["dropShadow", dropString],
      ["grayscale", grayscaleString],
      ["hue", hueString],
      ["invert", invertString],
      ["opacity", opacityString],
      ["saturate", saturateString],
      ["sepia", sepiaString],
    ]);
    //get active filterts
    let templist = initList.filter((str) => filterStringMap.get(str) !== "");
    //remove inactive filters
    list = list.filter((str) => filterStringMap.get(str) !== "");

    //concat active filters onto live list
    list = list.concat(templist.filter((active) => !list.includes(active)));
  }

  const sortList = (ev) => {
    list = ev.detail;
    console.log(list);
  };
  let styleSubstring;
  let styleString;
  $: {
    let filterStringMap = new Map([
      ["blur", blurString],
      ["brightness", brightnessString],
      ["contrast", contrastString],
      ["dropShadow", dropString],
      ["grayscale", grayscaleString],
      ["hue", hueString],
      ["invert", invertString],
      ["opacity", opacityString],
      ["saturate", saturateString],
      ["sepia", sepiaString],
    ]);
    styleSubstring = list.map((str) => filterStringMap.get(str)).join("\n");
    styleString = `filter:${styleSubstring};`;
  }
  function handleFileUpload(e) {
    var preview = imgDOM;
    var file = e.target.files[0];
    var reader = new FileReader();

    reader.onloadend = function () {
      preview.src = reader.result;
    };

    if (file) {
      reader.readAsDataURL(file);
    } else {
      preview.src = "";
    }
  }
</script>

<style>
  main {
    width: 100%;
    height: 100%;
  }
  img {
    width: 100%;
    height: 100%;
    object-fit: contain;
  }
  .output {
    position: fixed;
    bottom: 10px;
    left: 10px;
    width: 120px;
    font-size: 12px;
    user-select: all;
  }
  .input-container {
    font-size: 12px;
    position: fixed;
    top: 22px;
    right: 0;
    /* overflow: auto; */
  }
  input[type="file"] {
    position: fixed;
    top: 10px;
    left: 10px;
    z-index: 5;
  }
  input[type="number"] {
    position: relative;
    top: -24px;
    font-size: 12px;
    width: 55px;
  }
  input[type="range"] {
    position: relative;
    top: -6px;
  }
  input[type="color"] {
    padding: 0;
  }
  label {
    text-shadow: 0px 0px 2px white;
    margin-top: -20px;
    border-top: 1px solid black;
  }
  :global(p) {
    font-size: 12px;
    margin: 0;
  }
  .save {
    position: fixed;
    bottom: 10px;
    right: 10px;
    z-index: 5;
  }
</style>

<main>
  <input
    on:change={handleFileUpload}
    type="file"
    id="img"
    name="img"
    accept="image/png, image/jpeg, image/jpg" />
  <img bind:this={imgDOM} style={styleString} />
  <input
    on:change={handleFileUpload}
    type="file"
    id="img"
    name="img"
    accept="image/png, image/jpeg, image/jpg" />

  <pre class="output">{styleString}</pre>

  <button
    class="save"
    on:click={() => {
      screenshot(imgDOM);
    }}>save</button>

  <div class="input-container">
    <label>blur</label>
    <input bind:value={blur} type="range" min="0" max="50" step="0.1" />
    <input bind:value={blur} type="number" />

    <label>brightness</label>
    <input bind:value={brightness} type="range" min="0" max="11" step="0.1" />
    <input bind:value={brightness} type="number" />

    <label>contrast</label>
    <input bind:value={contrast} type="range" min="0" max="3" step="0.01" />
    <input bind:value={contrast} type="number" />

    <label>hue</label>
    <input bind:value={hue} type="range" min="0" max="360" step="1" />
    <input bind:value={hue} type="number" />

    <label>grayscale</label>
    <input bind:value={grayscale} type="range" min="0" max="1" step="0.01" />
    <input bind:value={grayscale} type="number" />

    <label>invert</label>
    <input bind:value={invert} type="range" min="0" max="1" step="0.01" />
    <input bind:value={invert} type="number" />

    <label>saturate</label>
    <input bind:value={saturate} type="range" min="0" max="11" step="0.1" />
    <input bind:value={saturate} type="number" />

    <label>opacity</label>
    <input bind:value={opacity} type="range" min="0" max="1" step="0.01" />
    <input bind:value={opacity} type="number" />

    <label>sepia</label>
    <input bind:value={sepia} type="range" min="0" max="1" step="0.01" />
    <input bind:value={sepia} type="number" />

    <label>drop-shadow x</label>
    <input bind:value={dropX} type="range" min="0" max="20" step="0.01" />
    <input bind:value={dropX} type="number" />
    <label>drop-shadow y</label>
    <input bind:value={dropY} type="range" min="0" max="20" step="0.01" />
    <input bind:value={dropY} type="number" />
    <label>drop-shadow blur</label>
    <input bind:value={dropBlur} type="range" min="0" max="50" step="0.01" />
    <input bind:value={dropBlur} type="number" />
    <label>drop-shadow color</label>
    <input bind:value={dropColor} type="color" />
    <br />
    <button on:click={initVals}>reset</button>
    <SortableList {list} on:sort={sortList} />
  </div>
</main>
