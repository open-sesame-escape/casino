---
title: "Security Time Lock"
draft: false
---

<div class="switch-container" id="switchContainer"></div>

<script>
  /* Seven Segment Display Setup */
  const SEGMENT_COORDS = {
    "a": "24,12 96,12 88,22 32,22",
    "b": "100,16 108,24 108,86 100,92 92,82 92,24",
    "c": "100,106 108,114 108,176 100,184 92,174 92,116",
    "d": "24,188 96,188 88,178 32,178",
    "e": "20,106 28,114 28,176 20,184 12,174 12,116",
    "f": "20,16 28,24 28,86 20,92 12,82 12,24",
    "g": "24,100 32,90 88,90 96,100 88,110 32,110"
  };

  const CHAR_MAP = {
    '0': 0b0111111,
    '1': 0b0000110,
    '2': 0b1011011,
    '3': 0b1001111,
    '4': 0b1100110,
    '5': 0b1101101,
    '6': 0b1111101,
    '7': 0b0000111,
    '8': 0b1111111,
    '9': 0b1101111,
    'A': 0b1110111,
    'b': 0b1111100,
    'C': 0b0111001,
    'd': 0b1011110,
    'E': 0b1111001,
    'F': 0b1110001,
    '-': 0b1000000,
    ' ': 0b0000000
  };

  let segments_list = []; // store polygon elements

  function createSevenSegmentSVG(parent) {
    const SVG_NS = "http://www.w3.org/2000/svg";
    const svg = document.createElementNS(SVG_NS, "svg");
    svg.setAttribute("viewBox", "0 0 120 200");
    svg.classList.add("seven-seg-svg");

    // optional background
    const bg = document.createElementNS(SVG_NS, "rect");
    bg.setAttribute("x", 4);
    bg.setAttribute("y", 6);
    bg.setAttribute("rx", 12);
    bg.setAttribute("width", 112);
    bg.setAttribute("height", 188);
    bg.setAttribute("fill", "#111a25");
    svg.appendChild(bg);

    // create the 7 segments
    let segments = {};
    for (const [name, pts] of Object.entries(SEGMENT_COORDS)) {
      const poly = document.createElementNS(SVG_NS, "polygon");
      poly.setAttribute("points", pts);
      poly.classList.add("segment");
      svg.appendChild(poly);
      segments[name] = poly;
    }
    segments_list.push(segments);

    parent.appendChild(svg);
  }

  /* Seven segment control functions */
  function setSegments(index, mask) {
    const keys = ["a","b","c","d","e","f","g"];
    const segments = segments_list[index];
    keys.forEach((key, i) => {
      if (mask & (1 << i)) segments[key].classList.add("on");
      else                 segments[key].classList.remove("on");
    });
  }

  function setValue(index, ch) {
    ch = (ch || " ").toUpperCase();

    const mask = CHAR_MAP[ch] ?? 0;
    setSegments(index, mask);
  }

  // Number of switches
  const numSwitches = 5;

  // Generate switches and lights dynamically
  const switchContainer = document.getElementById('switchContainer');

  digits = [ ['5', '0'], ['1', '7'], ['4','6'], ['3', '7'], ['3','6'] ];
  orientations = [ 'v', 'h', 'v', 'v', 'v' ]

  for (let i = 0; i < numSwitches; i++) {
    const digitWrapper = document.createElement('div');
    digitWrapper.classList.add('digit');

    createSevenSegmentSVG(digitWrapper);
    setValue(i, digits[i][0]);

    const switchWrapper = document.createElement('div');
    switchWrapper.classList.add('switch');

    const toggleSwitch = document.createElement('div');
    toggleSwitch.classList.add('toggle-switch-' + orientations[i]);
    toggleSwitch.id = `switch-${i}`;

    // Add event listener for toggling
    toggleSwitch.addEventListener('click', () => {
      const isOn = toggleSwitch.classList.toggle('on');
      setValue(i, digits[i][isOn ? 1 : 0])
    });

    switchWrapper.appendChild(toggleSwitch);
    digitWrapper.appendChild(switchWrapper);
    switchContainer.appendChild(digitWrapper);
  }
</script>
