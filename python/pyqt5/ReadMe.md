I found that tool that could do the trick on Steam OS.
```
sudo pacman -S python-pyqt5
```

```py

#!/usr/bin/env python3
# -*- coding: utf-8 -*-

import sys

from PyQt5.QtCore import Qt, QTimer
from PyQt5.QtGui import QColor, QPainter, QCursor
from PyQt5.QtWidgets import QApplication, QWidget


class MouseOverlay(QWidget):
    def __init__(self):
        super().__init__()

        self.square_size = 24

        # Transparent, frameless, always-on-top desktop overlay
        self.setWindowFlags(
            Qt.WindowStaysOnTopHint
            | Qt.FramelessWindowHint
            | Qt.Tool
            | Qt.WindowTransparentForInput
        )

        self.setAttribute(Qt.WA_TranslucentBackground)
        self.setAttribute(Qt.WA_ShowWithoutActivating)

        # Cover the virtual desktop, including multiple monitors
        screen = QApplication.primaryScreen()
        self.setGeometry(screen.virtualGeometry())

        # Poll the global mouse position
        self.timer = QTimer(self)
        self.timer.timeout.connect(self.update)
        self.timer.start(8)  # Approximately 125 updates per second

        self.show()

    def paintEvent(self, event):
        painter = QPainter(self)
        painter.setRenderHint(QPainter.Antialiasing, False)
        painter.setPen(Qt.NoPen)
        painter.setBrush(QColor(255, 0, 0))

        # Convert global mouse coordinates to overlay coordinates
        pos = self.mapFromGlobal(QCursor.pos())

        half = self.square_size // 2
        painter.drawRect(
            pos.x() - half,
            pos.y() - half,
            self.square_size,
            self.square_size
        )

        painter.end()


if __name__ == "__main__":
    app = QApplication(sys.argv)
    overlay = MouseOverlay()
    sys.exit(app.exec_())
```
