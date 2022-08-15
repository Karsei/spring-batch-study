# Skip & Retry Architecture

## ItemReader

![irf](./imgs/itemreader-flow.jpg)

예외가 발생한 아이템은 그냥 건너뛰고 그 다음의 아이템을 불러온다.

## ItemProcessor

![snr-ar-itemp](./imgs/skipnretry-itemprocessor-flow.jpg)

## ItemWriter

![snr-ar-itemw](./imgs/skipnretry-itemwriter-flow.jpg)

