<script>
  import { Button, TextareaInput, SuccessAlert, ErrorAlert } from "@codepiercer/svelte-tailwind"

  let xInput = `0.023, 0.045, 0.091, 0.181, 0.363, 0.726, 1.451, 2.902, 5.805, 11.610, 23.219`
  let yInput = `1.4221, 1.4148, 1.4028, 1.3767, 1.3317, 1.2742, 1.1790, 1.0671, 0.9309, 0.8047, 0.6696`
  let x = []
  let y = []
  let isLoading = false
  let success
  let error
  let allCurvatures = []

  const findMaxCurvature = () => {
    x = []
    y = []
    allCurvatures = []
    isLoading = true
    // Convert the input strings to arrays of numbers
    x = xInput
      .trim()
      .split(`,`)
      .map((x) => Number(x.trim()))
    y = yInput
      .trim()
      .split(`,`)
      .map((y) => Number(y.trim()))

    let maxCurvature = 0
    let maxCurvatureIndex = 0

    // Loop through all the data points
    for (let i = 2; i < x.length; i++) {
      // Calculate the curvature at this point
      const curvature = parseFloat(
        (y[i] - y[i - 1]) / (x[i] - x[i - 1]) - (y[i - 1] - y[i - 2]) / (x[i - 1] - x[i - 2])
      ).toFixed(4)
      allCurvatures.push(curvature)

      // Check if this is the maximum curvature so far
      if (curvature > maxCurvature) {
        maxCurvature = curvature
        maxCurvatureIndex = i - 1
      }
    }
    isLoading = false
    if (maxCurvatureIndex !== undefined) {
      success = `Max curvature is between points (${x[maxCurvatureIndex - 1]}, ${
        y[maxCurvatureIndex - 1]
      }) and (${x[maxCurvatureIndex]}, ${y[maxCurvatureIndex]})`
    } else {
      error = `No max curvature found`
    }
  }
</script>

<div class="flex h-screen w-full flex-col items-center justify-center gap-8">
  <div class="text-center">
    <h1>Welcome to p0</h1>
    <p>Arthur Casagrande's Graphical Method</p>
  </div>

  {#if isLoading}
    <div class="text-center">
      <p>Calculating...</p>
    </div>
  {/if}
  {#if success}
    <SuccessAlert>
      <p class="mb-2">{success}</p>
      {#each allCurvatures as curvature, idx}
        <p>Curvature at point ({x[idx]}, {y[idx]}): {curvature}</p>
      {/each}
    </SuccessAlert>
  {/if}
  {#if error}
    <ErrorAlert>
      <p>{error}</p>
    </ErrorAlert>
  {/if}

  <form id="form" on:submit|preventDefault={findMaxCurvature} class="flex flex-col gap-8">
    <div class="flex items-center gap-4">
      <TextareaInput
        isRequired
        label="X Points"
        bind:value={xInput}
        placeholder="Enter the x values"
      />
      <TextareaInput
        isRequired
        label="Y Points"
        bind:value={yInput}
        placeholder="Enter the y values"
      />
    </div>

    <Button form="form" type="submit">Find Max Curvature</Button>
  </form>
</div>
