# Workflow Size and Resource Notes

이 문서는 `example_workflows`의 workflow JSON에서 확인 가능한 입력/출력 크기, 프레임 수, 비디오/오디오 입력, VRAM 완화 장치를 정리한 것입니다.

## 결론

- workflow에는 대체로 `width`, `height`, `num_frames`, `steps`, block swap 값이 저장되어 있어 사양 부담을 추정할 수 있습니다.
- 하지만 정확한 VRAM 요구량은 workflow만으로 확정할 수 없습니다. 실제 모델 파일, dtype/quantization, GPU VRAM, attention backend, torch/driver 버전, 입력 비디오 길이, ComfyUI/노드 버전에 따라 달라집니다.
- 이 repo의 주요 생성 노드는 코드상 `width/height` 최대 8096, `num_frames` 최대 10000까지 UI 입력을 허용하지만, 이것은 실사용 가능하다는 뜻이 아니라 UI 제한입니다.
- `WanVideoImageToVideoEncode`는 프레임 수를 내부에서 `4n+1` 형태로 맞춥니다. 예: 80은 81 계열로 정렬됩니다.
- 14B, A14B, LongCat, WanAnimate, audio/video 입력 workflow는 사양을 많이 탑니다. 특히 121프레임 이상, 720p 이상, 500프레임급 오디오/애니메이션 workflow는 고사양 전제입니다.
- block swap, VRAM management, context/window, VAE tiling이 있으면 VRAM 부담을 줄일 수 있지만 속도 저하나 품질/경계 이슈가 생길 수 있습니다.

## 코드상 주요 제한과 의미

| 항목 | 코드상 제한/동작 | 의미 |
| --- | --- | --- |
| `WanVideoEmptyEmbeds.width/height` | min 64, max 8096, step 8 | 빈 latent/video target 크기. 실제 가능 크기는 VRAM에 좌우됨 |
| `WanVideoImageToVideoEncode.width/height` | min 64, max 8096, step 8 | I2V 입력 인코딩 목표 크기 |
| `WanVideoImageToVideoEncode.num_frames` | min 1, max 10000, step 4, 내부적으로 `4n+1` 정렬 | 긴 영상 가능성은 있지만 VRAM/시간 부담이 급증 |
| `WanVideoAnimateEmbeds.num_frames` | min 1, max 10000, step 4 | WanAnimate 장기 프레임 입력에 사용 |
| `WanVideoAnimateEmbeds.frame_window_size` | min 1, max 10000 | 긴 프레임을 window로 나눠 처리하는 힌트 |
| `WanVideoContextOptions.context_frames` | min 2, max 1000 | 긴 영상 sampling을 context window로 나누는 설정 |
| `WanVideoBlockSwap.blocks_to_swap` | max 48 | 14B는 40 blocks, 1.3B/5B는 30 blocks, LongCat은 48 blocks라는 tooltip이 있음 |
| `WanVideoVRAMManagement.offload_percent` | 0.0-1.0 | block swap보다 공격적인 offload 방식. 느려질 수 있음 |
| `WanVideoEncode/Decode` VAE tiling | tile/stride 지정 | VRAM 절감 가능. decode seam이 보일 수 있음 |

## Workflow별 설정 추정

`크기`는 생성 target, resize, image/video encode 노드에서 읽은 값입니다. `프레임`은 `num_frames`, `SampleFrames`, `frame_count`, context/animate 값 등 workflow에 저장된 값을 모은 것입니다. `위험도`는 모델 규모, 크기, 프레임 수, video/audio 입력, offload 장치 유무로 추정한 상대 지표입니다.

| Workflow | 모델 | 크기 힌트 | 프레임/길이 힌트 | steps | 완화 장치 | 입력 | 사양 부담 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `LongCatAvatar_audio_image_to_video_example_01.json` | LongCat | 832x480 | 93 | 12 | block swap 25 | audio | 중간 |
| `LongCat_TI2V_example_01.json` | LongCat | 832x480, 640x608 | 93 중심, 일부 1-frame init | 10, scheduler 122 | block swap 20 | image optional | 중간 |
| `wanvideo_1_3B_EchoShot_example.json` | 1.3B | 832x480 | 93 | 8 | 없음 | text | 낮음~중간 |
| `wanvideo_1_3B_FlashVSR_upscale_example.json` | 1.3B | 720x720, resize 1024x1024 | 85 | 1 | 없음 | video | 중간 |
| `wanvideo_1_3B_ReCamMaster_example_01.json` | 1.3B | 입력 video 기반 | video 길이 의존 | 20 | block swap 20, VRAM mgmt | video | 낮음~중간 |
| `wanvideo_1_3B_UniLumos_relight_example_01.json` | 1.3B | 640x640, 832x480 | video 길이 의존 | 6 | 없음 | video | 낮음~중간 |
| `wanvideo_1_3B_VACE_examples_03.json` | 1.3B | 512x512, 640x640 | control/reference video 길이 의존 | 20 | block swap node 있음 | video/images | 낮음~중간 |
| `wanvideo_1_3B_control_lora_example_01.json` | 1.3B | 입력 video 기반 | video 길이 의존 | 30 | 없음 | video/control | 낮음~중간 |
| `wanvideo_2_1_14B_FLF2V_720P_example_02.json` | 2.1 14B | 832x480, start/end resize 640x640/512x512 | 81 | 6 | block swap 20 | images | 중간 |
| `wanvideo_2_1_14B_Fun_control_camera_example_01.json` | 2.1 14B | 832x480, resize 624x624 | 81 | 30 | block swap 15 | image/camera | 중간 |
| `wanvideo_2_1_14B_Fun_control_example_01.json` | 2.1 14B | 832x480 | 81 | 25 | block swap 10, VRAM mgmt | video/control | 중간 |
| `wanvideo_2_1_14B_HuMo_example_01.json` | 2.1 14B | 1280x720 힌트, resize 512x512 | audio 길이 의존 | 8 | block swap 20 | audio/images | 중간~높음 |
| `wanvideo_2_1_14B_I2V_ATI_track_testing_01.json` | 2.1 14B | 832x480, resize 720x720 | 81 | 4 | block swap 10 | image/tracks | 중간 |
| `wanvideo_2_1_14B_I2V_example_03.json` | 2.1 14B | 832x480, resize 624x624 | 81 | 4 | block swap 10, VRAM mgmt | image | 중간 |
| `wanvideo_2_1_14B_I2V_FantasyPortrait_example_01.json` | 2.1 14B | 512x512, 720x720 | 81 | 6 | block swap 20 | image/video | 중간 |
| `wanvideo_2_1_14B_I2V_FantasyTalking_example_01.json` | 2.1 14B | 832x480, resize 512x512 | 81 | 30 | block swap 15, VRAM mgmt | image+audio | 중간~높음 |
| `wanvideo_2_1_14B_I2V_InfiniteTalk_example_03.json` | 2.1 14B | 640x640, resize 832x480 | max frames 500 | 6 | block swap 20 | image+audio | 높음 |
| `wanvideo_2_1_14B_I2V_SkyReelsV3_TalkingAvatar_example_01.json` | 2.1 14B | 832x480, 1088x832 | 81, max frames 500 | 4 | block swap 30 | image+audio | 높음 |
| `wanvideo_2_1_14B_MoCha_replace_subject_KJ_02.json` | 2.1 14B | 832x480, 983x480 | video 길이 의존 | 6 | block swap 40 | video/images | 중간 |
| `wanvideo_2_1_14B_MTV_Crafter_example_WIP.json` | 2.1 14B | 608x1088, 640x640 | 121, context 49/4/24 | 6 | block swap 10, context/window | video/pose | 중간~높음 |
| `wanvideo_2_1_14B_OneToAllAnimation_pose_control_example_01.json` | 2.1 14B | 480x832 | 81 | 6 | block swap 32 | video/pose | 중간 |
| `wanvideo_2_1_14B_phantom_subject2vid_example_02.json` | 2.1 14B | 832x480, resize 512x512 | image/reference 기반 | 8 | block swap 20 | images | 낮음~중간 |
| `wanvideo_2_1_14B_pusa_I2V_example_01.json` | 2.1 14B | 832x480, resize 720x720 | 81 | 6 | block swap 10 | image | 중간 |
| `wanvideo_2_1_14B_SCAIL_pose_control_example_01.json` | 2.1 14B | 480x1216, 512x896 | 65, context 81/4/48 | 6 | block swap 25, context/window | video/pose | 중간~높음 |
| `wanvideo_2_1_14B_skyreels_a2_example_01.json` | 2.1 14B | 832x480 | 81 | 25 | block swap 10, VRAM mgmt | multi-reference images | 중간 |
| `wanvideo_2_1_14B_skyreels_diffusion_forcing_extension_example_01.json` | 2.1 14B | 512x512 | 97, 53 windows/segments | sampler-specific | block swap 30, VRAM mgmt | optional video | 중간 |
| `wanvideo_2_1_14B_Stand-In_reference_example_01.json` | 2.1 14B | 832x480, resize 512x512 | 81 | 4 | block swap 20 | reference image | 중간 |
| `wanvideo_2_1_14B_SteadyDancer_pose_control_example_01.json` | 2.1 14B | 832x480, resize 480x832 | 81, context 81/4/16 | 4 | block swap 35, context/window | video/pose | 중간 |
| `wanvideo_2_1_14B_T2V_14B_lynx_example_01.json` | 2.1 14B | 832x480 | 121 | 6 | block swap 35 | face ref optional | 중간~높음 |
| `wanvideo_2_1_14B_T2V_example_03.json` | 2.1 14B | 832x480 | 81 | 6 | block swap 20 | text | 중간 |
| `wanvideo_2_1_14B_V2V_InfiniteTalk_example_02.json` | 2.1 14B | 640x640, resize 832x480 | max frames 1000 | 4 | block swap 20 | video+audio | 높음 |
| `wanvideo_2_1_14B_WanMove_I2V_example_01.json` | 2.1 14B | 832x480, resize 640x640 | 81 | 4 | block swap 25 | image/motion | 중간 |
| `wanvideo_2_2_14B_Pusa_extension_example_01.json` | 2.2 14B | 832x480 | 81, input frame count 24 | 6 | block swap 30 | video frames | 중간 |
| `wanvideo_2_2_5B_I2V_controlnet_example.json` | 2.2 5B | 832x480, 1280x704 preview/control hint | 121 | 30 | 없음 | image/video control | 높음 |
| `wanvideo_2_2_5B_I2V_example_WIP.json` | 2.2 5B | 832x480, resize 1024x1024 | 121 | 30 | 없음 | image | 중간~높음 |
| `wanvideo_2_2_5B_Ovi_image_to_video_audio_10_seconds_example_01.json` | 2.2 5B | 992x512, resize 1280x720 | 241 | 50 | block swap 34 | image+audio | 높음 |
| `wanvideo_2_2_5B_Ovi_image_to_video_audio_example_01.json` | 2.2 5B | 992x512, resize 704x704 | 121 | 50 | block swap 15 | image+audio | 중간~높음 |
| `wanvideo_2_2_5B_T2V_controlnet_example.json` | 2.2 5B | 1280x704 | 81/121 hint | 30 | 없음 | text+control video | 높음 |
| `wanvideo_2_2_Fun_control_camera_example_01.json` | 2.2 A14B | 832x480, 624x624 | 81 | 6 | block swap 30 | image/camera | 중간 |
| `wanvideo_2_2_Fun_control_example_03.json` | 2.2 A14B | 832x480, 480x832 | 81 | 6/30 split | block swap 10 | video/control | 중간 |
| `wanvideo_2_2_I2V_A14B_example_WIP.json` | 2.2 A14B | 832x480, resize 720x720 | 81 | 6 | block swap 20 | image | 중간 |
| `wanvideo_2_2_I2V_A14B_TimeToMove_example.json` | 2.2 A14B | 832x480 | 81 | 6 | block swap 20 | video/image motion | 중간 |
| `wanvideo_WanAnimate_example_01.json` | WanAnimate 14B | 832x480 | 501, context 81/4/32, animate window 77 | 6 | block swap 25, context/window | video+audio/images | 높음 |
| `wanvideo_WanAnimate_preprocess_example_02.json` | WanAnimate 14B | 832x480 | 501, context 81/4/32, animate window 77 | 4 | block swap 25, context/window | video+audio/images | 높음 |

## 비디오 소스 길이 제한에 대한 판단

- `VHS_LoadVideo` 등 외부 video loader 노드는 workflow 안에 저장된 widget 값이 비어 있거나 노드 버전에 따라 해석이 달라질 수 있습니다. 그래서 모든 workflow에서 명시적인 `frame_load_cap`을 확정할 수는 없습니다.
- 비디오 입력 workflow라도 이후 `WanVideoImageToVideoEncode`, `WanVideoContextOptions`, `WanVideoAnimateEmbeds`, `ImageBatch...` 노드가 프레임 수나 window를 제한/분할하는 경우가 많습니다.
- 명시적으로 긴 프레임을 쓰는 workflow:
  - InfiniteTalk I2V: max frames 500
  - InfiniteTalk V2V: max frames 1000
  - WanAnimate: 501 frames, context 81, window 77
  - Ovi 10 seconds: 241 frames
  - 2.2 5B 계열 일부: 121 frames
- 비디오 source가 긴 경우, loader에서 cap을 걸지 않으면 전처리/메모리 사용량이 크게 늘 수 있습니다. 긴 소스를 그대로 넣는 것보다 필요한 구간만 잘라서 넣는 것이 안전합니다.

## Workflow만으로 사양 체크가 가능한가?

가능한 것:

- 생성 목표 해상도와 프레임 수 추정
- 모델 규모 추정: 1.3B, 5B, 14B/A14B, LongCat, WanAnimate
- steps, context window, block swap, VRAM management, VAE tiling 존재 여부 확인
- video/audio 입력 여부 확인
- 상대적인 위험도 분류

불가능하거나 부정확한 것:

- 정확한 VRAM 요구량
- 실제 입력 비디오 전체 길이와 디코딩 비용
- 설치된 attention backend, GPU compute capability, CUDA/torch/driver 조건
- 모델 파일이 실제 fp8/gguf/bf16인지, 로컬 파일이 workflow와 같은지
- ComfyUI 실행 중 다른 노드/모델이 점유 중인 VRAM

따라서 이 표는 “실행 전 위험 신호를 보는 지도”로 쓰고, 실제 실행 가능 여부는 낮은 해상도/짧은 프레임/낮은 steps로 먼저 smoke test 하는 방식이 안전합니다.
