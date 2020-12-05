# Tracking Time with React Hooks

_#javascript, #react, #time, #datefns_

## Let's Talk About Time

Time is super tricky to account for in software, and one of the most common issues in frontend applications is developers forget that time keeps passing when the page is open.

> Take a look at [Falsehoods programmers believe about time](https://infiniteundo.com/post/25326999628/falsehoods-programmers-believe-about-time) for a starting point on the assumptions developers make.

It's really common to write a component that looks like this:

```js
const formatter = new Intl.DateTimeFormat("en-us", {
  year: "numeric",
  month: "numeric",
  day: "numeric",
  hour: "numeric",
  minute: "numeric",
  second: "numeric",
});

const MyDateComponent = () => {
  const date = new Date();

  return formatter.format(date);
};
```

> See [MDN Intl.DateTimeFormat](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/DateTimeFormat) for more on the date formatter used.

The issue with this component is that it doesn't update when the seconds change. This isn't as much an issue if we're not displaying seconds, but even hours and days can pass while browser tabs are open.

## `useDateTime`

To solve this problem, I wrote `useDateTime`, a React hook that tracks the time to a specified precision (second/minute/hour/day), triggering a state change on each `tick`.

Using `useDateTime` to fix `MyDateComponent`, we get the following:

```js
const MyDateComponent = () => {
  const date = useDateTime("second"); // second | minute | hour | day

  return formatter.format(date);
};
```

This component now updates every second, keeping it accurate. We probably only want updates for every second in a clock component, and should avoid this frequency of updates for expensive renders. Outside of clocks, updating by the hour/day is much more common and something we should plan for as frontend engineers.

You can take a look at the implementation of `useDateTime` in this codesandbox:

{% codesandbox usedatetime-example-icfmq module=/src/use-datetime.js view=editor %}

_The implementation uses [`date-fns`](https://github.com/date-fns/date-fns) but could be rewritten with any date library (e.g. Moment/Luxon/Day.js)_

> A similar hook could be used to track relative times by modifying `msUntilNext` to determine the next `tick` in increasing intervals.

### Disclaimer

This component _attempts_ to update immediately after the next `tick` at the specified precision. Javascript's `setTimeout` API does not guarantee that the timeout will trigger precisely on the target time, so the precision of this hook is also limited. Here's a good [Stack Overflow Q&A](https://stackoverflow.com/questions/29971898/how-to-create-an-accurate-timer-in-javascript) summarizing this problem and a workaround.
