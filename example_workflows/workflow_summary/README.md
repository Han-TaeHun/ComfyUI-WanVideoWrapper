# Example Workflow Summary

이 문서는 `example_workflows` 폴더의 ComfyUI workflow JSON 44개를 빠르게 고르기 위한 요약입니다.
정리는 파일명, workflow 내부 노드 타입, 노드 제목/메모, 모델 파일명을 함께 보고 작성했습니다.

## 빠른 분류

| 분류 | 워크플로우 |
| --- | --- |
| 기본 T2V/I2V | `wanvideo_2_1_14B_T2V_example_03.json`, `wanvideo_2_1_14B_I2V_example_03.json`, `wanvideo_2_2_5B_I2V_example_WIP.json`, `wanvideo_2_2_I2V_A14B_example_WIP.json` |
| ControlNet/Control/Camera | `wanvideo_1_3B_control_lora_example_01.json`, `wanvideo_2_1_14B_Fun_control_example_01.json`, `wanvideo_2_1_14B_Fun_control_camera_example_01.json`, `wanvideo_2_2_5B_I2V_controlnet_example.json`, `wanvideo_2_2_5B_T2V_controlnet_example.json`, `wanvideo_2_2_Fun_control_example_03.json`, `wanvideo_2_2_Fun_control_camera_example_01.json` |
| Pose/Motion/Track | `wanvideo_2_1_14B_I2V_ATI_track_testing_01.json`, `wanvideo_2_1_14B_OneToAllAnimation_pose_control_example_01.json`, `wanvideo_2_1_14B_SCAIL_pose_control_example_01.json`, `wanvideo_2_1_14B_SteadyDancer_pose_control_example_01.json`, `wanvideo_2_1_14B_WanMove_I2V_example_01.json`, `wanvideo_2_2_I2V_A14B_TimeToMove_example.json`, `wanvideo_WanAnimate_example_01.json`, `wanvideo_WanAnimate_preprocess_example_02.json` |
| Audio/Talking Avatar | `LongCatAvatar_audio_image_to_video_example_01.json`, `wanvideo_2_1_14B_HuMo_example_01.json`, `wanvideo_2_1_14B_I2V_FantasyTalking_example_01.json`, `wanvideo_2_1_14B_I2V_InfiniteTalk_example_03.json`, `wanvideo_2_1_14B_V2V_InfiniteTalk_example_02.json`, `wanvideo_2_1_14B_I2V_SkyReelsV3_TalkingAvatar_example_01.json`, `wanvideo_2_2_5B_Ovi_image_to_video_audio_example_01.json`, `wanvideo_2_2_5B_Ovi_image_to_video_audio_10_seconds_example_01.json` |
| Reference/Subject/Portrait | `wanvideo_2_1_14B_I2V_FantasyPortrait_example_01.json`, `wanvideo_2_1_14B_MoCha_replace_subject_KJ_02.json`, `wanvideo_2_1_14B_phantom_subject2vid_example_02.json`, `wanvideo_2_1_14B_Stand-In_reference_example_01.json`, `wanvideo_2_1_14B_skyreels_a2_example_01.json` |
| Extension/Edit/Relight/Upscale | `wanvideo_1_3B_EchoShot_example.json`, `wanvideo_1_3B_FlashVSR_upscale_example.json`, `wanvideo_1_3B_UniLumos_relight_example_01.json`, `wanvideo_1_3B_VACE_examples_03.json`, `wanvideo_2_1_14B_FLF2V_720P_example_02.json`, `wanvideo_2_1_14B_skyreels_diffusion_forcing_extension_example_01.json`, `wanvideo_2_2_14B_Pusa_extension_example_01.json` |
| LongCat/Lynx/Pusa/MTV 특화 | `LongCat_TI2V_example_01.json`, `wanvideo_2_1_14B_T2V_14B_lynx_example_01.json`, `wanvideo_2_1_14B_pusa_I2V_example_01.json`, `wanvideo_2_1_14B_MTV_Crafter_example_WIP.json` |

## 워크플로우별 요약

| 파일 | 주 기능 | 주요 노드/특징 |
| --- | --- | --- |
| `LongCatAvatar_audio_image_to_video_example_01.json` | LongCat Avatar 기반 오디오+이미지 입력 비디오 생성 | `WanVideoLongCatAvatarExtendEmbeds`, `TrimAudioDuration`, `WanVideoSamplerv2`, LongCat Avatar 모델, wav2vec/audio embeds 사용 |
| `LongCat_TI2V_example_01.json` | LongCat TI2V 텍스트/이미지 투 비디오 | `WanVideoSamplerSettings`, `WanVideoSamplerFromSettings`, `WanVideoSetLoRAs`; 메모상 T2V는 `extra_latents` 연결 해제 |
| `wanvideo_1_3B_control_lora_example_01.json` | Wan 1.3B Control LoRA 예제 | `WanVideoControlEmbeds`, control LoRA, 입력 비디오/이미지를 조건으로 사용하는 기본 control 흐름 |
| `wanvideo_1_3B_EchoShot_example.json` | EchoShot 1.3B T2V 예제 | EchoShot 전용 1.3B 모델과 distill/self-forcing LoRA, `WanVideoEnhanceAVideo`, `WanVideoExperimentalArgs` 포함 |
| `wanvideo_1_3B_FlashVSR_upscale_example.json` | FlashVSR 업스케일/초해상도 | `WanVideoAddFlashVSRInput`, `WanVideoFlashVSRDecoderLoader`, extra model select로 저해상도 입력을 VSR 처리 |
| `wanvideo_1_3B_ReCamMaster_example_01.json` | ReCamMaster 카메라 경로 제어 | `WanVideoReCamMasterDefaultCamera`, `WanVideoReCamMasterGenerateOrbitCamera`, `WanVideoReCamMasterCameraEmbed`, camera pose visualizer |
| `wanvideo_1_3B_UniLumos_relight_example_01.json` | UniLumos relighting | `WanVideoUniLumosEmbeds`로 입력 영상/이미지의 조명 조건을 바꾸는 relight 흐름 |
| `wanvideo_1_3B_VACE_examples_03.json` | VACE 기반 편집/참조/컨트롤 예제 묶음 | `WanVideoVACEEncode`, `WanVideoVACEModelSelect`, `WanVideoVACEStartToEndFrame`; start/end/reference/control video 사용 |
| `wanvideo_2_1_14B_FLF2V_720P_example_02.json` | First/Last Frame to Video 720p | 시작 이미지와 종료 이미지를 조건으로 중간 영상을 생성, `WanVideoImageToVideoEncode`, CLIP vision 사용 |
| `wanvideo_2_1_14B_Fun_control_camera_example_01.json` | WanVideo Fun camera control | `WanVideoFunCameraEmbeds`로 이미지 기반 생성에 카메라 움직임 조건을 추가 |
| `wanvideo_2_1_14B_Fun_control_example_01.json` | WanVideo Fun control | depth 등 preprocessor control signal을 `WanVideoControlEmbeds`로 연결하는 control workflow |
| `wanvideo_2_1_14B_HuMo_example_01.json` | HuMo 오디오/레퍼런스 기반 생성 | audio 입력, whisper/MelBandRoFormer 계열 모델, reference image를 사용하는 HuMo workflow |
| `wanvideo_2_1_14B_I2V_ATI_track_testing_01.json` | ATI 포인트/트랙 기반 I2V | `WanVideoATITracks`, `WanVideoATITracksVisualize`; 샘플 포인트/트랙으로 이미지 내 움직임 제어 |
| `wanvideo_2_1_14B_I2V_example_03.json` | 기본 Wan 2.1 14B I2V | 이미지 입력, CLIP vision, `WanVideoImageToVideoEncode`, text embed bridge를 사용하는 표준 I2V |
| `wanvideo_2_1_14B_I2V_FantasyPortrait_example_01.json` | FantasyPortrait 인물 애니메이션 | `FantasyPortraitModelLoader`, `FantasyPortraitFaceDetector`, `WanVideoAddFantasyPortrait`; 얼굴/초상화 특화 |
| `wanvideo_2_1_14B_I2V_FantasyTalking_example_01.json` | FantasyTalking 오디오 기반 talking portrait | `FantasyTalkingModelLoader`, `FantasyTalkingWav2VecEmbeds`; 이미지+오디오로 말하는 인물 영상 생성 |
| `wanvideo_2_1_14B_I2V_InfiniteTalk_example_03.json` | InfiniteTalk 단일 이미지 talking video | `WanVideoImageToVideoMultiTalk`, audio frames, wav2vec, clip vision optional 메모 포함 |
| `wanvideo_2_1_14B_I2V_SkyReelsV3_TalkingAvatar_example_01.json` | SkyReels V3 talking avatar | 먼저 일반 I2V 프레임을 만들고, 오디오용 참조 프레임으로 이어 쓰는 2단계 구조 |
| `wanvideo_2_1_14B_MoCha_replace_subject_KJ_02.json` | MoCha subject replacement | `WanVideoModelLoader`, multi LoRA, reference/subject replacement 목적의 이미지 기반 workflow |
| `wanvideo_2_1_14B_MTV_Crafter_example_WIP.json` | MTV Crafter pose/motion 예제 | pose video 입력, `WanVideoContextOptions`, image-to-video, LoRA 연결이 있는 WIP motion workflow |
| `wanvideo_2_1_14B_OneToAllAnimation_pose_control_example_01.json` | OneToAll pose-controlled animation | `WanVideoAddOneToAllPoseEmbeds`, `WanVideoAddOneToAllReferenceEmbeds`; pose/reference 조건으로 애니메이션 생성 |
| `wanvideo_2_1_14B_phantom_subject2vid_example_02.json` | Phantom subject-to-video | `WanVideoPhantomEmbeds`; subject/reference 이미지에서 비디오를 생성하는 Phantom 모델 흐름 |
| `wanvideo_2_1_14B_pusa_I2V_example_01.json` | Pusa I2V LoRA 예제 | Pusa LoRA와 Lightx2v LoRA를 연결해 I2V 품질/속도를 조정하는 workflow |
| `wanvideo_2_1_14B_SCAIL_pose_control_example_01.json` | SCAIL pose control | SCAIL pose embeds/LoRA 계열을 사용해 pose 조건으로 비디오 생성 |
| `wanvideo_2_1_14B_skyreels_a2_example_01.json` | SkyReels A2 multi-reference | background/subject/reference 이미지 여러 장을 조합하는 다중 참조 workflow |
| `wanvideo_2_1_14B_skyreels_diffusion_forcing_extension_example_01.json` | SkyReels diffusion forcing extension | `WanVideoDiffusionForcingSampler`; T2V/I2V/V2V 입력을 이어 붙여 계속 확장하는 구조 |
| `wanvideo_2_1_14B_Stand-In_reference_example_01.json` | Stand-In reference identity | `WanVideoAddStandInLatent`; reference identity/subject 정보를 latent 조건으로 추가 |
| `wanvideo_2_1_14B_SteadyDancer_pose_control_example_01.json` | SteadyDancer pose control | pose/control video와 reference image로 춤/동작을 전이하는 workflow |
| `wanvideo_2_1_14B_T2V_14B_lynx_example_01.json` | Lynx T2V/IP face reference | `LynxEncodeFaceIP`, `LoadLynxResampler`; face reference를 곁들인 Lynx 계열 T2V |
| `wanvideo_2_1_14B_T2V_example_03.json` | 기본 Wan 2.1 14B T2V | text encoder, empty image embeds, scheduler/sampler, VAE decode로 이어지는 표준 T2V |
| `wanvideo_2_1_14B_V2V_InfiniteTalk_example_02.json` | InfiniteTalk V2V audio workflow | video/image sequence와 audio를 함께 사용하는 InfiniteTalk video-to-video 변형 |
| `wanvideo_2_1_14B_WanMove_I2V_example_01.json` | WanMove I2V motion control | WanMove motion/control 노드와 I2V 모델을 결합해 이미지에 움직임 조건 부여 |
| `wanvideo_2_2_14B_Pusa_extension_example_01.json` | Wan 2.2 Pusa video extension | HIGH/LOW A14B 모델, Pusa HIGH/LOW LoRA, `WanVideoAddExtraLatent`, `WanVideoSigmaToStep`로 기존 프레임 확장 |
| `wanvideo_2_2_5B_I2V_controlnet_example.json` | Wan 2.2 5B I2V ControlNet | `WanVideoControlnetLoader`, `WanVideoControlnet`, depth controlnet, EasyCache 포함 |
| `wanvideo_2_2_5B_I2V_example_WIP.json` | Wan 2.2 5B 기본 I2V WIP | TI2V 5B 모델로 이미지 입력을 latent화해 생성하는 기본 WIP 예제 |
| `wanvideo_2_2_5B_Ovi_image_to_video_audio_10_seconds_example_01.json` | Ovi 10초 이미지+오디오 생성 | `OviMMAudioVAELoader`, `WanVideoEmptyMMAudioLatents`, `WanVideoDecodeOviAudio`; 10초 모델용 audio latent 메모 포함 |
| `wanvideo_2_2_5B_Ovi_image_to_video_audio_example_01.json` | Ovi 이미지+오디오 생성 | Ovi video/audio model과 MMAudio VAE/vocoder를 사용해 비디오와 오디오를 함께 생성/디코드 |
| `wanvideo_2_2_5B_T2V_controlnet_example.json` | Wan 2.2 5B T2V ControlNet | text-to-video에 depth ControlNet 조건을 붙이는 구조, PreviewAnimation 포함 |
| `wanvideo_2_2_Fun_control_camera_example_01.json` | Wan 2.2 Fun camera control | HIGH/LOW A14B + camera control model, camera path visualization, split step 설정 |
| `wanvideo_2_2_Fun_control_example_03.json` | Wan 2.2 Fun control | depth 등 control signal, HIGH/LOW control 모델, Lightning/Lightx2v LoRA를 함께 쓰는 control workflow |
| `wanvideo_2_2_I2V_A14B_example_WIP.json` | Wan 2.2 A14B 기본 I2V WIP | HIGH/LOW I2V A14B 모델과 split step/steps 설정을 사용하는 기본 I2V |
| `wanvideo_2_2_I2V_A14B_TimeToMove_example.json` | TimeToMove drag/motion I2V | `WanVideoAddTTMLatents`; TimeToMove 예제 입력 기반으로 특정 영역/포인트 움직임을 유도 |
| `wanvideo_WanAnimate_example_01.json` | WanAnimate character animation | reference/background/pose/face/audio/video 입력, `WanVideoAnimateEmbeds`, SAM2 segmentation 포함 |
| `wanvideo_WanAnimate_preprocess_example_02.json` | WanAnimate preprocess + generation | WanAnimate 생성 흐름에 pose/face/mask 전처리 링크와 SAM2/VitPose 관련 노드를 포함 |

## 예제 입력 파일

`example_inputs` 폴더에는 일부 workflow가 참조하는 테스트 미디어가 들어 있습니다.

| 파일 | 용도 추정 |
| --- | --- |
| `env.png` | 배경/환경 참조 이미지 |
| `human.png` | 인물 참조 이미지 |
| `thing.png` | 객체/subject 참조 이미지 |
| `woman.jpg` | talking avatar 또는 portrait 입력 이미지 |
| `woman.wav` | talking avatar/audio-driven workflow 입력 음성 |
| `jeep.mp4` | video-to-video/control 테스트 입력 영상 |
| `MTV_crafter_example_pose.mp4` | MTV/pose control 테스트 입력 영상 |
| `wolf_interpolated.mp4` | VACE/control/video extension 계열 테스트 입력 영상 |

## 읽는 방법

- 빠르게 일반 생성 예제를 찾을 때는 기본 T2V/I2V 항목부터 본다.
- 이미지나 비디오로 움직임을 통제하려면 ControlNet/Control/Camera 또는 Pose/Motion/Track 항목을 본다.
- 오디오로 말하는 얼굴/아바타를 만들려면 Audio/Talking Avatar 항목을 본다.
- 특정 인물/subject 일관성이 필요하면 Reference/Subject/Portrait 항목을 본다.
- 기존 영상을 이어 붙이거나 편집하려면 Extension/Edit/Relight/Upscale 항목을 본다.
