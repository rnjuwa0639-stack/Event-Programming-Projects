# Bug Fix Documentation

## Bug 1: Blocking the EDT
- **Symptom:** UI freezes when button clicked
- **Cause:** Long operation runs on EDT
- **Fix:** Run operation in background thread, update UI on EDT
- **Code Change:** Moved the `Thread.sleep(5000)` call out of the button's `ActionListener` and into a `new Thread(() -> {...}).start()` so it no longer runs on the EDT. After the sleep finishes, the label update (`statusLabel.setText("Done!")`) is wrapped in `SwingUtilities.invokeLater(() -> {...})` so it runs back on the EDT. I also disabled the button and showed "Processing..." while the work runs, then re-enabled it afterwards.

## Bug 2: Missing Listener
- **Symptom:** Button click does nothing
- **Cause:** No ActionListener registered
- **Fix:** Added ActionListener to increment button
- **Code Change:** Added `incrementButton.addActionListener(e -> { count++; countLabel.setText("Count: " + count); });` in the constructor. Each click now increases `count` by 1 and refreshes the label to show the new value.

## Bug 3: Wrong Thread
- **Symptom:** Erratic behavior, potential crashes
- **Cause:** GUI updated from background thread
- **Fix:** Used SwingUtilities.invokeLater() for GUI updates
- **Code Change:** Wrapped `timeLabel.setText("Time: " + new Date());` inside the background thread's loop in `SwingUtilities.invokeLater(() -> {...})`. The `Thread.sleep(1000)` stays on the background thread, so only the label update runs on the EDT.

## Bug 4: Memory Leak
- **Symptom:** Memory usage grows over time
- **Cause:** References kept in list after removal from UI
- **Fix:** Clear the list when removing buttons
- **Code Change:** Added `createdButtons.clear();` in the "Remove All" listener, right after `buttonPanel.removeAll();`. The list now drops its references to the removed buttons, so they can be garbage collected.