# LSM6DSV FIFO recovery investigation

Observed recovery path after FIFO overrun:

- FIFO status corruption can be caused by SPI interface state loss (for example IF_INC not active), not only FIFO overflow.
- A future recovery path should treat repeated `FIFO_STATUS1/2 == 0xff/0xff` as communication failure.
- Recommended recovery sequence:
  1. Restore SPI interface state (`CTRL3 = BDU | IF_INC`).
  2. Read WHO_AM_I / status register to validate communication.
  3. Put FIFO into bypass mode.
  4. Clear FIFO and restore continuous mode.
  5. Re-enable FIFO interrupt.

This note is temporary investigation documentation for the FIFO overrun fix.
