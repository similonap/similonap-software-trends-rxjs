## Angular RxJS Examples

### Observables

The `ObservablesComponent` demonstrates the use of RxJS observables. Create the following observables:
- `observable1$`: An observable that emits the numbers 1, 2, and 3 sequentially and then completes.
- `observable2$`: An observable that emits an incrementing number time every second. Create the observable yourself using the `Observable` constructor not the `interval` function.
- `observable3$`: Now create an observable that emits a number every second using the `interval` function.

Create a button per observable that subscribes to the observable when clicked. Display the emitted values in the console. Create another button to unsubscribe from the observable.

As an additional challenge, figure out how to create a hot observable and subscribe to it. Make sure you understand the difference between hot and cold observables.


<video controls>
  <source src="Observables.mp4" type="video/mp4" />
</video>

## Stopwatch component

Use the knowledge of observables to create a stopwatch component. The stopwatch should have the following functionalities:
- Start: Start the stopwatch.
- Stop: Stop the stopwatch.
- Reset: Reset the stopwatch to 0.
- Lap: Record the current time and display it in a list below the stopwatch.

Use reactive programming to implement the stopwatch functionalities. You can use the `interval` function to create an observable that emits a number every second.

Link video: [Stopwatch](Stopwatch.mp4)

### Click Component

The `ClickComponent` provides an interactive UI where the user can click a button to dynamically change the size and color of a box. These changes are throttled using RxJS's `debounceTime` to update only after 500 milliseconds of inactivity. The size and color of the box increase with each click, and the updates are applied smoothly using Angular's `AsyncPipe`.

Below is a breakdown of how this component works:

1. **User Click Handling:**
   - A button is displayed in the UI with an `(click)` event bound to the `click` method.
   - Each click triggers the `click` method, which pushes a value (in this case, `1`) into an RxJS `Subject` stream.

2. **Observable for Box Size:**
   - The `boxSize$` observable is responsible for updating the size of the box:
     - `scan((acc, value) => acc + value, 0)`: This operator accumulates the clicks over time, increasing the size by adding the click value to a running total.
     - `debounceTime(500)`: This operator ensures that the box's size is updated only after 500 milliseconds of no further clicks. It acts as a delay to reduce rapid updates.
     - `map((value) => value * 5)`: The accumulated click count is multiplied by 5 to increase the size of the box proportionally.
     - `filter((value) => value <= document.body.clientWidth)`: This ensures the box size does not exceed the width of the browser window.

3. **Observable for Box Color:**
   - The `boxColor$` observable dynamically adjusts the color of the box based on the number of clicks:
     - `scan((acc, value) => acc + value, 0)`: Similar to the `boxSize$` observable, this operator accumulates the click count.
     - `debounceTime(500)`: The color is updated only after 500 milliseconds of inactivity, ensuring a smooth transition.
     - `map((value) => `rgb(${value * 10 % 255}, 100, 150)`): This maps the accumulated value to a dynamic RGB color. The red component of the color is calculated by multiplying the click count by 10 and taking the modulo of 255, while the green and blue components remain constant.

4. **Template Rendering with AsyncPipe:**
   - The component template uses Angular's `AsyncPipe` to subscribe to the `boxSize$` and `boxColor$` observables:
     - The size of the box (both width and height) is dynamically updated with the value from `boxSize$`.
     - The background color of the box is dynamically updated with the value from `boxColor$`.
   - The updates occur asynchronously and are reflected in the DOM without requiring manual subscription or handling of the observables.

5. **Debounced Updates:**
   - Both the size and color of the box are updated after a 500ms delay following the last click. This prevents rapid updates and ensures smoother transitions.

Link video: [ClickComponent](Resize.mp4)

### Search Component

The SearchComponent allows users to search for countries in real-time by typing a query into an input field. The component uses RxJS operators to handle input changes and send API requests to fetch matching countries after debouncing the user's input. Below are the key functionalities and flow for this component:

1. **User Input Handling:**

- The user types into a search input field bound to an onChange event handler.
- This handler takes the input value from the event and pushes it into an RxJS Subject stream.

2. **RxJS Stream Processing:**

- The Subject stream emits the values typed by the user and is processed through an RxJS pipeline in the ngOnInit method.
- The stream goes through several operators:
    - debounceTime(300): Pauses and waits for 300ms of inactivity before processing the latest input.
    - filter(value => value.length >= 2): Filters out any input that is less than 2 characters in length to reduce unnecessary API requests.
    - distinctUntilChanged(): Ensures only new and different values trigger an action, ignoring repeated identical inputs.
    - switchMap(value => this.fetchCountries(value)): Cancels any ongoing API request if a new input is detected, ensuring only the latest search value is used.

3. **Fetching Countries:**

- The fetchCountries method sends an HTTP GET request to a country search API, with the query parameter based on the user's input. `https://sampleapis.assimilate.be/countries/countries?name_like=` is the API endpoint used.
- This request is retried up to 3 times, with a 1-second delay between retries, using the retry operator to handle temporary errors.

4. **Updating the View:**

- The response from the API (an array of countries) is assigned to the countries array.
- The component automatically updates the view to display the list of matching countries.
- The component automatically updates the view to display the list of matching countries.

Link video: [Search Debounce](./SearchDebounce.mp4)
