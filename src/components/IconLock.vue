<template>
  <svg
    width="24"
    height="24"
    viewBox="0 0 24 24"
    fill="none"
    xmlns="http://www.w3.org/2000/svg"
    xmlns:xlink="http://www.w3.org/1999/xlink"
  >
    <rect width="24" height="24" fill="url(#pattern4)" />
    <defs>
      <pattern id="pattern4" patternContentUnits="objectBoundingBox" width="1" height="1">
        <use xlink:href="#image4" transform="scale(0.0166667)" />
      </pattern>
      <image
        id="image4"
        width="60"
        height="60"
        xlink:href="data:image/png;base64,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"
      />
    </defs>
  </svg>
</template>
