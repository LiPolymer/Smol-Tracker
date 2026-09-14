# LSM6DSV FIFO recovery state machine

Recovery order:

1. Detect invalid FIFO status separately from empty FIFO.
2. Restore SPI interface settings (BDU + IF_INC).
3. Verify communication using WHO_AM_I.
4. Reset FIFO into bypass mode.
5. Restore continuous FIFO mode and batching configuration.
6. Only escalate to reboot when recovery fails.

The sensor loop should treat transient communication faults differently from normal FIFO under-run.
